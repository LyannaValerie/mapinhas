---
title: Multiplexação de IO
aliases:
  - I/O multiplexing
  - multiplexação de E/S
  - select
  - fd_set
  - event-driven
  - programação orientada a eventos
  - servidor orientado a eventos
tags:
  - computação/arquitetura
date: 2026-04-14
source: "CS:APP - Cap. 12"
---

# Multiplexação de IO

## Definição
Técnica de programação concorrente onde uma única aplicação monitora múltiplos descritores de arquivo simultaneamente, progredindo apenas quando algum descriptor estiver pronto para leitura/escrita. Elimina a necessidade de múltiplos processos ou threads para lidar com múltiplos clientes.

## Relações (SPO)
- Multiplexação de IO → usa → função `select()` do kernel
- `select()` → suspende processo até → um ou mais descritores estarem prontos
- Multiplexação de IO → modela fluxos lógicos como → máquinas de estado
- Multiplexação de IO → roda em → processo único (sem fork/thread)
- Servidor event-driven → usa → [[Multiplexação de IO]] como base
- Multiplexação de IO → usada em → Node.js, nginx, Tornado
- Multiplexação de IO → comparada com → [[Processo]] e [[Thread]] como abordagem concorrente

---

## Problema motivador

Servidor echo que também aceita comandos do stdin:
- Bloquear em `accept()` → não responde ao stdin
- Bloquear em `read()` → não aceita novas conexões

**Solução:** `select()` — aguarda o que vier primeiro.

---

## API: select()

```c
#include <sys/select.h>

int select(int n, fd_set *fdset, NULL, NULL, NULL);
// Returns: número de descritores prontos (> 0); -1 em erro
// n: maior descritor + 1 (cardinalidade máxima)
// fdset: "read set" — conjunto de descritores a monitorar
// Efeito colateral: modifica fdset → mostra "ready set"
```

> [!warning] Efeito colateral de select
> `select` sobrescreve `fdset` com o subconjunto pronto. Sempre copiar o read_set original antes de cada chamada.

### Macros para fd_set (vetor de bits)

| Macro | Operação |
|---|---|
| `FD_ZERO(fd_set *fdset)` | Zera todos os bits |
| `FD_SET(int fd, fd_set *fdset)` | Ativa bit do descritor `fd` |
| `FD_CLR(int fd, fd_set *fdset)` | Desativa bit do descritor `fd` |
| `FD_ISSET(int fd, fd_set *fdset)` | Testa se bit `fd` está ativo |

### Exemplo mínimo

```c
fd_set read_set, ready_set;
FD_ZERO(&read_set);
FD_SET(STDIN_FILENO, &read_set);  // stdin = 0
FD_SET(listenfd, &read_set);

while (1) {
    ready_set = read_set;           // preserva read_set original
    Select(listenfd + 1, &ready_set, NULL, NULL, NULL);

    if (FD_ISSET(STDIN_FILENO, &ready_set))
        command();                  // processa comando do usuário

    if (FD_ISSET(listenfd, &ready_set)) {
        connfd = Accept(listenfd, ...);
        echo(connfd);               // ainda iterativo — limitação
        Close(connfd);
    }
}
```

---

## Programação orientada a eventos (event-driven)

Fluxos lógicos são modelados como **máquinas de estado**:

```
Estado:       "aguardando descritor dk estar pronto"
Evento:       "descritor dk pronto para leitura"
Transição:    "lê linha de texto de dk"
```

Para cada cliente k: uma máquina de estado com descritor associado dk.

### Estrutura pool para servidor concorrente

```c
typedef struct {
    int maxfd;           // maior descritor no read_set
    fd_set read_set;     // conjunto de todos os descritores ativos
    fd_set ready_set;    // subconjunto pronto (retornado por select)
    int nready;          // quantidade de descritores prontos
    int maxi;            // índice máximo ativo em clientfd[]
    int clientfd[FD_SETSIZE];    // descritores de clientes ativos (-1 = livre)
    rio_t clientrio[FD_SETSIZE]; // buffers de leitura por cliente
} pool;
```

**Funções principais:**
- `init_pool(listenfd, &pool)` — inicializa pool com só listenfd no read_set
- `add_client(connfd, &pool)` — adiciona novo cliente ao pool e ao read_set
- `check_clients(&pool)` — processa uma linha de cada cliente pronto; fecha ao detectar EOF

**Loop principal:**

```c
init_pool(listenfd, &pool);
while (1) {
    pool.ready_set = pool.read_set;
    pool.nready = Select(pool.maxfd + 1, &pool.ready_set, NULL, NULL, NULL);

    if (FD_ISSET(listenfd, &pool.ready_set))
        add_client(Accept(listenfd, ...), &pool);

    check_clients(&pool);
}
```

---

## Comparação com outras abordagens concorrentes

| Aspecto | Processos | I/O Multiplexing | Threads |
|---|---|---|---|
| Escalonamento | Kernel | **Aplicação** (explícito) | Kernel |
| Espaço de endereçamento | **Separado** | Compartilhado | Compartilhado |
| Compartilhamento de dados | Difícil (IPC) | **Fácil** | **Fácil** |
| Depuração | Difícil | **Fácil** (processo único) | Moderada |
| Overhead de context switch | Alto | **Zero** | Baixo |
| Controle sobre fluxo | Baixo | **Alto** | Médio |
| Complexidade de código | Baixa | **Alta** | Média |
| Suporte a multi-core | Sim | **Não** | Sim |
| Clientes maliciosos (linha parcial) | Seguro | **Vulnerável** | Seguro |

---

## Vantagens

- Programador controla explicitamente o escalonamento dos fluxos — permite priorizar clientes
- Processo único: todos os fluxos acessam o mesmo espaço de endereçamento → fácil compartilhar dados
- Depuração com `gdb` como qualquer programa sequencial
- Sem overhead de context switch entre processos/threads
- Alta performance: Node.js, nginx, Tornado usam este modelo

---

## Desvantagens

- Código significativamente mais complexo (~3× mais que abordagem baseada em processos)
- Complexidade aumenta à medida que a granularidade de concorrência diminui
- Vulnerável a clientes maliciosos que enviam linhas parciais (bloqueia todo o servidor)
- **Não pode explorar múltiplos núcleos** — executa em processo único

> [!note] Granularidade de concorrência
> A granularidade é o número de instruções executadas por fluxo por time slice. Enquanto um fluxo lê uma linha completa, nenhum outro pode progredir. Aplicações que exigem granularidade fina precisam de lógica extra para lidar com leituras parciais.

---

## Ver também
- [[Processo]] — programação concorrente com processos (fork + SIGCHLD)
- [[Thread]] — programação concorrente com threads (Pthreads); thread safety; races; deadlocks
- [[Semáforo]] — sincronização de threads com P/V, mutex, produtor-consumidor
- [[Sockets]] — base para servidores de rede que usam multiplexação de IO
- [[Chamada do Sistema]] — select() é system call que envolve o kernel
