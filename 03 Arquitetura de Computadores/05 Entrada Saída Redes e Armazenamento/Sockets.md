---
title: Sockets
aliases:
  - socket
  - socket descriptor
  - sockfd
  - interface de sockets
  - sockets interface
  - programação de rede
  - open_clientfd
  - open_listenfd
  - getaddrinfo
  - getnameinfo
  - sockaddr_in
tags:
  - computação/arquitetura
date: 2026-04-14
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Sockets

Interface de programação de rede do Unix. Socket = **extremidade de uma conexão**. Do ponto de vista do kernel: end point de comunicação. Do ponto de vista do programa: **arquivo aberto com descritor correspondente** — um socket descriptor.

Implementada em todos os sistemas Unix, Windows e macOS.

**Fonte:** CS:APP Cap. 11 — Network Programming (Bryant & O'Hallaron)

## Relações (SPO)
- Socket → é do ponto de vista do programa → arquivo aberto com [[Descritores de Arquivo UNIX|descritor de arquivo]]
- Socket → modela → conexão TCP entre cliente e servidor
- `socket()` → cria → socket descriptor parcialmente aberto
- `connect()` → termina abertura de → socket do cliente
- `bind/listen/accept()` → terminam abertura de → socket do servidor
- `getaddrinfo` → converte → hostname/serviço → estrutura de endereço de socket
- [[Descritores de Arquivo UNIX]] → usado para ler/escrever → socket descriptor (após conexão estabelecida)
- [[Rede]] → implementa o meio físico → sobre o qual sockets operam

---

## Estruturas de endereço

```c
/* Endereço de socket IP (Internet) */
struct sockaddr_in {
    uint16_t       sin_family; /* AF_INET */
    uint16_t       sin_port;   /* Número de porta (network byte order) */
    struct in_addr sin_addr;   /* Endereço IP 32 bits (network byte order) */
    unsigned char  sin_zero[8]; /* Padding para sizeof(struct sockaddr) */
};

/* Endereço genérico (usado em connect, bind, accept) */
struct sockaddr {
    uint16_t sa_family;  /* Família de protocolo */
    char     sa_data[14];/* Dados do endereço */
};
typedef struct sockaddr SA; /* Alias conveniente para cast */
```

IP e porta são armazenados em **network byte order (big-endian)**.

---

## Fluxo cliente-servidor

```
Cliente                          Servidor
getaddrinfo                      getaddrinfo
    │                                │
socket()                         socket()
    │                            bind()
    │                            listen()
    │                                │ ← aguarda conexão
connect() ──── requisição ──→   accept() ← retorna connfd
    │           de conexão           │
    │◄── conexão estabelecida ───────│
    │                                │
rio_writen ──── dados ──────→   rio_readlineb
rio_readlineb ←── eco/resposta ── rio_writen
    │                                │
close() ──── EOF ──────────→    rio_readlineb retorna 0
                                 close()
```

---

## Funções da Interface de Sockets

### `socket` — criar descritor

```c
#include <sys/types.h>
#include <sys/socket.h>

int socket(int domain, int type, int protocol);
/* Retorna: fd não-negativo se OK, -1 em erro */

/* Exemplo: */
clientfd = Socket(AF_INET, SOCK_STREAM, 0);
```

Retorna socket descriptor **parcialmente aberto** — não pode ser usado para I/O ainda. Usar `getaddrinfo` para gerar os parâmetros automaticamente (portabilidade).

---

### `connect` — cliente estabelece conexão

```c
#include <sys/socket.h>

int connect(int clientfd, const struct sockaddr *addr, socklen_t addrlen);
/* Retorna: 0 se OK, -1 em erro */
```

Bloqueia até conexão estabelecida ou erro. Após sucesso, `clientfd` está pronto para I/O. Par de socket resultante: `(x:y, addr.sin_addr:addr.sin_port)` onde `y` é porta efêmera do cliente.

---

### `bind` — servidor associa endereço ao socket

```c
#include <sys/socket.h>

int bind(int sockfd, const struct sockaddr *addr, socklen_t addrlen);
/* Retorna: 0 se OK, -1 em erro */
```

Associa endereço de socket do servidor ao descritor `sockfd`.

---

### `listen` — converter para socket passivo

```c
#include <sys/socket.h>

int listen(int sockfd, int backlog);
/* Retorna: 0 se OK, -1 em erro */
```

Converte `sockfd` de **active socket** (padrão do kernel) para **listening socket**. `backlog` = fila de conexões pendentes (tipicamente 1024). Servidor é entidade passiva; cliente é entidade ativa.

---

### `accept` — servidor aceita conexão

```c
#include <sys/socket.h>

int accept(int listenfd, struct sockaddr *addr, int *addrlen);
/* Retorna: fd conectado não-negativo se OK, -1 em erro */
```

Bloqueia aguardando conexão em `listenfd`. Retorna **connected descriptor** (`connfd`) — novo fd para comunicar com este cliente específico.

**Distinção crítica:**

| Tipo | Criação | Vida útil |
|---|---|---|
| **Listening descriptor** | `socket + bind + listen`, uma vez | Todo o ciclo de vida do servidor |
| **Connected descriptor** | `accept`, a cada conexão | Duração do atendimento ao cliente |

Essa distinção permite servidores concorrentes: `fork` por conexão, cada filho usa seu `connfd`.

---

## Conversão de Hostname/Serviço

### `getaddrinfo` — hostname → estrutura de socket

```c
#include <sys/types.h>
#include <sys/socket.h>
#include <netdb.h>

int getaddrinfo(const char *host, const char *service,
                const struct addrinfo *hints,
                struct addrinfo **result);
/* Retorna: 0 se OK, código de erro não-zero em erro */

void freeaddrinfo(struct addrinfo *result);
const char *gai_strerror(int errcode);
```

Converte hostname/endereço + serviço/porta → lista encadeada de `addrinfo` (e `sockaddr` dentro de cada entrada).

```c
struct addrinfo {
    int              ai_flags;
    int              ai_family;      /* Passado a socket() */
    int              ai_socktype;    /* Passado a socket() */
    int              ai_protocol;   /* Passado a socket() */
    char            *ai_canonname;
    size_t           ai_addrlen;    /* Passado a connect/bind() */
    struct sockaddr *ai_addr;       /* Passado a connect/bind() */
    struct addrinfo *ai_next;
};
```

**Campos de `hints` úteis:**

| Campo/Flag | Uso |
|---|---|
| `ai_family = AF_INET` | Somente IPv4 |
| `ai_socktype = SOCK_STREAM` | Somente conexões (1 entry por endereço) |
| `AI_ADDRCONFIG` | Retorna IPv4 se host tem IPv4; recomendado para clientes |
| `AI_PASSIVE` + `host = NULL` | Wildcard address para servidores (aceita qualquer IP do host) |
| `AI_CANONNAME` | Preenche `ai_canonname` com nome canônico |
| `AI_NUMERICSERV` | `service` é número de porta (não nome) |

Campos de `addrinfo` são **opacos** — passados diretamente a `socket`, `connect`, `bind`.

---

### `getnameinfo` — estrutura de socket → hostname/serviço

```c
#include <sys/socket.h>
#include <netdb.h>

int getnameinfo(const struct sockaddr *sa, socklen_t salen,
                char *host, size_t hostlen,
                char *service, size_t servlen, int flags);
/* Retorna: 0 se OK, código de erro não-zero em erro */
```

Inverso de `getaddrinfo`. Útil para logar endereço de cliente após `accept`.

Flags úteis: `NI_NUMERICHOST` (retorna string de endereço em vez de domínio), `NI_NUMERICSERV` (retorna número de porta).

---

## Funções helper (csapp)

### `open_clientfd`

```c
int open_clientfd(char *hostname, char *port);
/* Retorna: fd aberto se OK, -1 em erro */
```

Encapsula `getaddrinfo + socket + connect`. Retorna fd pronto para I/O Unix.

### `open_listenfd`

```c
int open_listenfd(char *port);
/* Retorna: fd ouvinte se OK, -1 em erro */
```

Encapsula `getaddrinfo + socket + bind + listen`. Wildcard address via `AI_PASSIVE`.

Usa `setsockopt(SO_REUSEADDR)` para permitir restart imediato do servidor (sem espera de 30s).

---

## Sockets e Unix I/O

Sockets são arquivos Linux — referenciados por descritores, lidos/escritos via `read`/`write` ou [[Descritores de Arquivo UNIX|RIO]].

> [!warning] Usar RIO, não standard I/O, para sockets
> Standard I/O é inadequada para sockets:
> - `lseek` é proibido em sockets
> - Restrição de intercalação input/output requer `fflush`/`fseek` — impossível em socket
>
> Usar `rio_readn`, `rio_writen`, `rio_readlineb` para I/O em sockets.

**SIGPIPE:** se servidor escreve em socket já fechado pelo cliente, primeiro `write` retorna normalmente; segundo entrega `SIGPIPE` (comportamento padrão: termina o processo). Servidor robusto deve capturar `SIGPIPE` e verificar `EPIPE` em chamadas `write`.

---

## Ver também
- [[Descritores de Arquivo UNIX]] — `read`/`write`/`dup2`; RIO; compartilhamento de arquivo; `dup2` para CGI
- [[Rede]] — modelo cliente-servidor; Internet; adaptador de rede como dispositivo de E/S
- [[Processo]] — `fork` por conexão para servidores concorrentes; herança de descritores
- [[Sinais]] — `SIGPIPE`/`EPIPE` em escritas para sockets fechados
- [[Concorrência e Paralelismo]] — servidores concorrentes processam múltiplos clients via `fork`
- [[Transmissão de Dados]] — TCP/IP; endereços IP; DNS
