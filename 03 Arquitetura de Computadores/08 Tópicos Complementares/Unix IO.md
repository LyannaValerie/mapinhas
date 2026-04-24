---
title: Unix I/O
aliases:
  - Unix I/O
  - unix i/o
  - system-level I/O
  - I/O unix
  - E/S unix
  - descritor de arquivo
  - file descriptor
  - RIO
  - rio_readn
  - rio_writen
  - rio_readlineb
  - dup2
  - redirecionamento de I/O
  - compartilhamento de arquivo
  - v-node
  - tabela de arquivos abertos
tags:
  - computação/arquitetura
date: 2026-04-14
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Unix I/O

Interface de E/S de nível de sistema do kernel Linux. Todo dispositivo de E/S — disco, terminal, rede — é modelado como arquivo. Toda E/S é leitura/escrita nesses arquivos. Isso permite uma interface uniforme e de baixo nível: Unix I/O.

**Fonte:** CS:APP Cap. 10 — System-Level I/O (Bryant & O'Hallaron)

## Relações (SPO)
- Unix I/O → modela como arquivo → todo dispositivo de E/S (disco, terminal, rede, socket)
- Processo → identifica arquivo aberto via → descritor de arquivo (inteiro não-negativo)
- Unix I/O → é base de → [[Sistema de Arquivos|standard I/O library]] (libc) e RIO package
- Compartilhamento de arquivo → usa → tabela de descritores + tabela de arquivos + tabela v-node
- `dup2` → implementa → redirecionamento de I/O no [[Shell]]
- Unix I/O → é usado por → [[Sockets|programação de rede]] (standard I/O inadequada para sockets)

---

## 10.1 Unix I/O

Arquivo Linux = sequência de `m` bytes: `B₀, B₁, ..., Bₘ₋₁`.

Operações fundamentais:

| Operação | Descrição |
|---|---|
| **Abrir** (`open`) | Kernel retorna **descritor** — inteiro não-negativo que identifica o arquivo |
| **Alterar posição** (`lseek`) | Kernel mantém posição `k` (byte offset inicial = 0); seek reposiciona |
| **Ler** (`read`) | Copia `n > 0` bytes a partir de `k`; incrementa `k` por `n`; EOF quando `k ≥ m` |
| **Escrever** (`write`) | Copia `n > 0` bytes para o arquivo a partir de `k`; atualiza `k` |
| **Fechar** (`close`) | Libera estruturas internas do kernel; devolve descritor ao pool disponível |

Descritores especiais presentes em todo processo:

| fd | Constante | Dispositivo |
|---|---|---|
| 0 | `STDIN_FILENO` | Entrada padrão |
| 1 | `STDOUT_FILENO` | Saída padrão |
| 2 | `STDERR_FILENO` | Saída de erro |

> [!note] Sem caractere EOF
> Não existe caractere especial de EOF. EOF é uma condição detectada quando `read` retorna 0 (posição ≥ tamanho do arquivo).

---

## 10.2 Tipos de arquivo Linux

| Tipo | Descrição |
|---|---|
| **Regular** | Sequência arbitrária de bytes; kernel não distingue texto de binário |
| **Diretório** | Array de links `(nome → arquivo)`; contém `.` e `..` |
| **Socket** | Arquivo para comunicar com outro processo via rede (Cap. 11) |
| Outros | Named pipes, symbolic links, char/block devices |

**EOL:** Linux usa `\n` (0x0a). Windows e HTTP usam `\r\n` (0x0d 0x0a).

---

## 10.3 Abrindo e Fechando Arquivos

```c
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>

int open(char *filename, int flags, mode_t mode);
/* Retorna: novo fd se OK, -1 em erro */
```

`open` retorna sempre o menor fd disponível.

**Flags de acesso:**

| Flag | Significado |
|---|---|
| `O_RDONLY` | Somente leitura |
| `O_WRONLY` | Somente escrita |
| `O_RDWR` | Leitura e escrita |
| `O_CREAT` | Cria arquivo se não existir (truncado) |
| `O_TRUNC` | Trunca arquivo existente |
| `O_APPEND` | Posiciona no fim antes de cada escrita |

**Bits de permissão (mode):**

| Máscara | Permissão |
|---|---|
| `S_IRUSR/IWUSR/IXUSR` | Leitura/escrita/execução pelo dono |
| `S_IRGRP/IWGRP/IXGRP` | Leitura/escrita/execução pelo grupo |
| `S_IROTH/IWOTH/IXOTH` | Leitura/escrita/execução por outros |

`umask`: permissões efetivas = `mode & ~umask`.

```c
#include <unistd.h>
int close(int fd);
/* Retorna: 0 se OK, -1 em erro */
/* Fechar descritor já fechado é erro */
```

---

## 10.4 Lendo e Escrevendo Arquivos

```c
#include <unistd.h>
ssize_t read(int fd, void *buf, size_t n);
/* Retorna: bytes lidos se OK, 0 em EOF, -1 em erro */

ssize_t write(int fd, const void *buf, size_t n);
/* Retorna: bytes escritos se OK, -1 em erro */
```

> [!warning] Short counts
> `read`/`write` podem transferir **menos bytes que o solicitado** — não é erro. Ocorre em:
> - EOF próximo (leitura de disco)
> - Terminal: read retorna uma linha por vez
> - Socket de rede: buffering interno + atrasos de rede
>
> Para arquivos em disco: short count só ocorre em EOF. Para redes: deve-se repetir `read`/`write` até transferir todos os bytes solicitados.

---

## 10.5 Pacote RIO (Robust I/O)

Wrappers que tratam short counts automaticamente. Desenvolvido para programas de rede. Thread-safe.

### Funções sem buffer (unbuffered)

```c
#include "csapp.h"
ssize_t rio_readn(int fd, void *usrbuf, size_t n);
ssize_t rio_writen(int fd, void *usrbuf, size_t n);
/* Retorna: bytes transferidos se OK, 0 em EOF (só readn), -1 em erro */
```

- `rio_readn`: pode retornar short count apenas em EOF
- `rio_writen`: **nunca** retorna short count
- Ambas reiniciam `read`/`write` automaticamente se interrompidas por sinal (`EINTR`)

### Funções com buffer (buffered)

```c
void rio_readinitb(rio_t *rp, int fd);     /* Inicializa buffer; 1× por fd */
ssize_t rio_readlineb(rio_t *rp, void *usrbuf, size_t maxlen);
ssize_t rio_readnb(rio_t *rp, void *usrbuf, size_t n);
/* Retorna: bytes lidos se OK, 0 em EOF, -1 em erro */
```

Buffer interno `rio_t`: 8192 bytes (`RIO_BUFSIZE`).

```c
typedef struct {
    int  rio_fd;                 /* Descritor */
    int  rio_cnt;                /* Bytes não lidos no buffer */
    char *rio_bufptr;            /* Próximo byte não lido */
    char rio_buf[RIO_BUFSIZE];   /* Buffer interno */
} rio_t;
```

`rio_readlineb` e `rio_readnb` são intercambiáveis no mesmo fd. **Não misturar** com `rio_readn` (sem buffer).

---

## 10.6 Metadados de Arquivo (stat)

```c
#include <unistd.h>
#include <sys/stat.h>

int stat(const char *filename, struct stat *buf);
int fstat(int fd, struct stat *buf);
/* Retorna: 0 se OK, -1 em erro */
```

Campos relevantes de `struct stat`:

| Campo | Conteúdo |
|---|---|
| `st_mode` | Tipo do arquivo + bits de permissão |
| `st_size` | Tamanho em bytes |
| `st_ino` | i-node |
| `st_atime/mtime/ctime` | Timestamps de acesso/modificação/mudança |

**Macros de tipo** (em `sys/stat.h`):

| Macro | Tipo |
|---|---|
| `S_ISREG(m)` | Arquivo regular |
| `S_ISDIR(m)` | Diretório |
| `S_ISSOCK(m)` | Socket de rede |

---

## 10.7 Lendo Conteúdo de Diretório

```c
#include <sys/types.h>
#include <dirent.h>

DIR    *opendir(const char *name);     /* Abre stream de diretório */
struct dirent *readdir(DIR *dirp);     /* Retorna próxima entrada; NULL = fim ou erro */
int     closedir(DIR *dirp);           /* Fecha stream */
```

Estrutura de entrada:

```c
struct dirent {
    ino_t d_ino;       /* número do i-node */
    char  d_name[256]; /* nome do arquivo */
};
```

Para distinguir fim-de-stream de erro: verificar se `errno` foi modificado após `readdir` retornar `NULL`.

---

## 10.8 Compartilhamento de Arquivo

O kernel usa **três tabelas** para representar arquivos abertos:

```
Processo A                    Kernel (global)
┌──────────────────┐    ┌─────────────────────┐    ┌──────────────────┐
│ Tabela de        │    │ Tabela de arquivos   │    │ Tabela v-node    │
│ descritores      │    │ abertos (file table) │    │                  │
│ (por processo)   │    │                      │    │                  │
│                  │    │ ┌──────────────────┐ │    │ ┌──────────────┐ │
│ fd 0 ──────────────→  │ │ pos_arquivo      │ │ →  │ │ st_mode      │ │
│ fd 1 ──────────────→  │ │ refcnt           │ │    │ │ st_size      │ │
│ fd 2            │    │ │ ptr v-node ──────────→  │ │ tipo         │ │
│ fd 3 ──────────────→  │ └──────────────────┘ │    │ └──────────────┘ │
└──────────────────┘    └─────────────────────┘    └──────────────────┘
```

| Tabela | Escopo | Conteúdo |
|---|---|---|
| **Tabela de descritores** | Por processo | fd → entrada na file table |
| **File table** | Global (todos processos) | posição atual, `refcnt`, ptr → v-node |
| **V-node table** | Global | campos de `stat` (`st_mode`, `st_size`, etc.) |

**Regra de fechamento:** kernel deleta entrada da file table somente quando `refcnt == 0`.

### Casos de compartilhamento

1. **Dois `open` do mesmo arquivo** → dois file table entries distintos → posições independentes
2. **`fork`** → filho recebe cópia da tabela de descritores do pai; ambos **compartilham** mesmas entradas da file table → mesma posição; `refcnt` incrementado para cada fd

---

## 10.9 Redirecionamento de I/O

```c
#include <unistd.h>
int dup2(int oldfd, int newfd);
/* Retorna: fd não-negativo se OK, -1 em erro */
```

`dup2(oldfd, newfd)`: copia entrada `oldfd` da tabela de descritores para `newfd`, sobrescrevendo. Se `newfd` estava aberto, fecha primeiro.

**Exemplo — redirecionar stdout para arquivo:**
```
Antes: fd 1 → file table de terminal A (refcnt=1)
       fd 4 → file table de disco B   (refcnt=1)

dup2(4, 1):
Depois: fd 1 → file table de disco B  (refcnt=2)
        fd 4 → file table de disco B  (refcnt=2)
        file table de terminal A deletada (refcnt=0)
```

Shell usa `dup2` para implementar `ls > foo.txt`. [[Processo|CGI servers]] usam `dup2(connfd, STDOUT_FILENO)` para redirecionar stdout para o socket do cliente.

---

## 10.10 Standard I/O vs Unix I/O vs RIO

```
Programa C
    │
    ├─── Standard I/O (libc): fopen, fread, fwrite, fgets, fputs, printf, scanf
    │         │ usa internamente
    ├─── RIO: rio_readn, rio_writen, rio_readlineb, rio_readnb
    │         │ usa internamente
    └─── Unix I/O (syscalls): open, read, write, close, stat, lseek
                  │ implementado no kernel
```

### Diretrizes de uso

| Situação | Recomendação |
|---|---|
| Disco ou terminal | Standard I/O (preferência) |
| Rede (sockets) | **RIO** — standard I/O tem restrições incompatíveis com sockets |
| Metadados de arquivo | Unix I/O diretamente (`stat`) — sem equivalente em standard I/O |
| Dados binários | Não usar `scanf`/`rio_readlineb` — confunde bytes `0x0a` com `\n` |

**Por que standard I/O é inadequada para sockets:**
- Proibido usar `lseek` em sockets
- Restrição 1: input após output requer `fflush` (ou `fseek/fsetpos/rewind`)
- Restrição 2: output após input requer `fseek/fsetpos/rewind` — impossível em socket

---

## Ver também
- [[Sistema de Arquivos]] — i-nodes, tabela de descritores, tipos de arquivo, syscalls `open/read/write/close`
- [[Processo]] — tabela de descritores por processo; herança de fds via `fork`
- [[Chamada do Sistema]] — Unix I/O é implementado inteiramente via syscalls
- [[Sockets]] — socket = arquivo Unix; Rio recomendado para I/O em sockets
- [[Mapeamento de Memória]] — `mmap` como alternativa a `read`/`write` para arquivos
- [[Sinais]] — `SIGPIPE`/`EPIPE` ao escrever em socket fechado
- [[Shell]] — `dup2` implementa operadores `>` e `<` de redirecionamento
