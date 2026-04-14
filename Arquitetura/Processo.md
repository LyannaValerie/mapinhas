---
title: Processo
aliases:
  - process
  - processos
  - contexto de processo
  - context switching
  - troca de contexto
  - thread
  - kernel
  - zombie
  - processo zumbi
  - reaping
tags:
  - computação/arquitetura
date: 2026-04-06
---

# Processo

## Definição
Abstração do [[Sistema Operacional]] para um **programa em execução**. Cada processo tem a ilusão de uso exclusivo do [[Processador]], da [[Memória]] principal e dos dispositivos de E/S — mesmo quando vários processos rodam simultaneamente no mesmo sistema.

## Relações (SPO)
- Processo → é uma abstração de → [[Sistema Operacional]]
- Processo → abstrai → [[Processador]] + [[Memória]] + dispositivos de E/S
- Processo → identificado por → PID (Process ID)
- Processo → seu estado é → contexto (PC + registradores + memória + estruturas do kernel)
- Processo → gerenciado por → [[#Kernel|kernel]] do SO
- Processo → pode criar filho via → `fork()`
- Processo → pode substituir imagem via → `execve()`
- Processo → termina filho zumbi via → `waitpid()`
- Processo → pode receber → [[Sinais|sinal]]
- Processo → pode conter → múltiplas [[#Threads|threads]]

---

## A ilusão do processo

Quando um programa roda em um sistema moderno, o SO fornece duas ilusões fundamentais:
1. **Fluxo de controle lógico exclusivo** — parece ter o processador só para si
2. **Espaço de endereçamento privado** — parece ter a memória toda para si

Essas ilusões são possíveis graças ao conceito de processo — uma das ideias mais importantes e bem-sucedidas da ciência da computação.

---

## Fluxo de controle lógico

Cada processo tem seu próprio **fluxo lógico** — a sequência de valores do PC que ele observa. Processos diferentes têm fluxos independentes que se intercalam no tempo.

Dois processos são **concorrentes** se seus fluxos se sobrepõem no tempo (mesmo que em um único processador). Se rodarem em paralelo em núcleos diferentes, são **paralelos**.

---

## Espaço de endereçamento privado

Cada processo tem seu próprio **espaço de endereçamento privado** — um byte do processo não pode ser lido/escrito por outro processo. Todos os espaços têm a mesma organização geral em x86-64 Linux:

```
2^64 - 1  ┌─────────────────────────┐
           │  Kernel (código, dados, │  ← sempre residente; invisível ao usuário
           │  heap, pilha do kernel) │
           ├─────────────────────────┤
           │  Pilha do usuário       │  ← cresce para baixo; expandida em runtime
           │    (runtime stack)      │
           ├─────────────────────────┤
           │  Bibliotecas            │
           │  compartilhadas (mmap)  │
           ├─────────────────────────┤
           │  Heap                   │  ← cresce para cima via malloc/brk
           ├─────────────────────────┤
           │  Dados (BSS, data)      │
           ├─────────────────────────┤
0          │  Código (text)          │
           └─────────────────────────┘
```

O kernel reside no mesmo espaço de endereçamento de todos os processos, mas é protegido — código em modo usuário não pode ler/escrever nem executar código do kernel. Ver [[Proteção de Memória x86]].

---

## PID e identidade do processo

Todo processo tem um **PID** (Process ID) único. O processo que o criou é o **pai**; seu PID é o PPID.

```c
#include <sys/types.h>
#include <unistd.h>
pid_t getpid(void);   // PID do processo atual
pid_t getppid(void);  // PID do pai
```

---

## Estados do processo

| Estado | Descrição |
|---|---|
| **Running** | Executando na CPU ou aguardando ser escalado |
| **Stopped** | Suspenso por sinal (SIGSTOP, SIGTSTP) — não será escalado até receber SIGCONT |
| **Terminated** | Encerrado permanentemente — por `exit()`, sinal, ou retorno do `main` |

---

## Contexto

O kernel mantém o **contexto** de cada processo — tudo necessário para reiniciar um processo preemptado:

- Registradores de uso geral
- Registradores de ponto flutuante
- PC (program counter)
- Pilha do usuário
- Registradores de estado
- Pilha do kernel
- Estruturas de dados do kernel:
  - **Tabela de páginas** — caracteriza o espaço de endereçamento
  - **Tabela de processos** — informações do processo atual
  - **Tabela de arquivos** — arquivos abertos

---

## Troca de contexto (Context Switching)

Como um único processador pode executar múltiplos processos, o SO realiza **troca de contexto** — mecanismo construído sobre o mecanismo de [[Interrupção|exceção]] de baixo nível:

1. Salva o contexto do processo atual
2. Restaura o contexto de um processo preemptado anteriormente
3. Passa controle para o processo restaurado

**O kernel decide trocar quando:**
- Syscall **bloqueia** (ex: `read` aguardando disco → kernel troca para outro processo enquanto DMA transfere)
- **Interrupção de timer** periódica (tipicamente 1–10 ms) — kernel pode decidir trocar mesmo que a syscall não bloqueie

**Fluxo típico (read + DMA):**
```
Processo A (usuário) → read() → trap → kernel A
kernel A → inicia DMA, dorme → context switch → kernel B
Processo B (usuário) executa
disco conclui → interrupção → context switch → kernel A
kernel A retorna read → Processo A (usuário)
```

> [!info] Processos concorrentes vs. paralelos
> Uniprocessador: processos são **concorrentes** (alternância rápida). Multicore: processos podem ser **paralelos** (execução simultânea real).

---

## Controle de Processos

### fork — criar processo filho

```c
#include <sys/types.h>
#include <unistd.h>
pid_t fork(void);
// Retorna: PID do filho ao pai; 0 ao filho; -1 em erro
```

`fork()` cria um novo processo filho com:
- **Cópia idêntica** do espaço de endereçamento do pai (código, dados, heap, pilha)
- **Mesmos descritores de arquivo** abertos
- **PID diferente** do pai

> [!important] fork retorna duas vezes
> Chamado uma vez, retorna **duas vezes** — uma no pai (retorna PID do filho), uma no filho (retorna 0). Pai e filho são **concorrentes** — a ordem de execução entre eles é não-determinística.

```c
pid_t pid = fork();
if (pid == 0) {
    // código do filho
} else {
    // código do pai — pid contém PID do filho
}
```

### execve — substituir imagem do processo

```c
#include <unistd.h>
int execve(const char *filename, const char *argv[], const char *envp[]);
// Nunca retorna se bem-sucedido; retorna -1 em erro
```

`execve()` carrega e executa um novo programa no contexto do processo atual — substitui código, dados, pilha e heap. **Não cria novo processo**. PID permanece o mesmo.

Chamado uma vez, **nunca retorna** (se bem-sucedido).

### waitpid — aguardar e colher filho

```c
#include <sys/types.h>
#include <sys/wait.h>
pid_t waitpid(pid_t pid, int *statusp, int options);
// Retorna: PID do filho encerrado; -1 em erro
```

- `pid > 0`: aguarda filho específico
- `pid = -1`: aguarda qualquer filho do processo
- `options = 0`: bloqueia até qualquer filho encerrar
- `options = WNOHANG`: retorna imediatamente se nenhum filho encerrou
- `options = WUNTRACED`: também retorna se filho foi *stopped*

Macros para inspecionar `statusp`:
| Macro | Verdadeiro quando |
|---|---|
| `WIFEXITED(status)` | filho encerrou normalmente |
| `WEXITSTATUS(status)` | código de saída (se `WIFEXITED`) |
| `WIFSIGNALED(status)` | filho encerrou por sinal |
| `WTERMSIG(status)` | sinal que causou término |
| `WIFSTOPPED(status)` | filho está stopped |

### Processo zumbi (zombie)

Quando um processo encerra, o kernel **não o remove imediatamente** — mantém um estado mínimo (PID, status de saída) para que o pai possa coletar via `waitpid()`. Esse processo encerrado-mas-não-coletado é um **zumbi**.

- Pai que nunca chama `waitpid()` acumula zumbis → vazamento de recursos
- Se o pai encerra antes do filho, o filho é **adotado pelo processo `init`** (PID 1), que periodicamente faz `waitpid()` — os zumbis são então coletados

> [!warning] Shell e zumbis
> Um shell bem implementado instala um handler para `SIGCHLD` que chama `waitpid()` para colher todos os filhos encerrados. Sem isso, cada processo filho vira um zumbi permanente.

---

## Kernel

O **kernel** é a parte do código do SO que está **sempre residente na memória**. É responsável por:
- Gerenciar todos os processos
- Realizar trocas de contexto
- Executar operações solicitadas via [[Chamada do Sistema]]

> [!note] Kernel não é um processo separado
> O kernel é uma **coleção de código e estruturas de dados** usada pelo sistema para gerenciar processos — não um processo em si.

---

## Threads

Um processo pode conter múltiplas **threads** — unidades de execução que rodam no contexto do processo, **compartilhando** o mesmo código e dados globais.

| | Processo | Thread |
|---|---|---|
| Código e dados | Isolados | **Compartilhados** com outras threads do mesmo processo |
| Overhead de criação | Alto | Baixo |
| Compartilhamento de dados | Complexo (IPC) | Simples (memória compartilhada) |
| Concorrência | Sim | Sim (mais eficiente que múltiplos processos) |

> [!info] Threads e paralelismo real
> Uniprocessador: threads são *concorrentes*. Multicore/hyperthreading: threads podem executar *em paralelo*. Ver [[Concorrência e Paralelismo]].

---

## Ver também
- [[Fluxo de Controle Excepcional]] — processos e sinais como ECF em nível SO/aplicação
- [[Sinais]] — mecanismo de notificação entre processos e kernel
- [[Sistema Operacional]] — o processo é uma das três abstrações fundamentais do SO
- [[Memória Virtual]] — cada processo tem sua própria visão da memória
- [[Chamada do Sistema]] — como processos solicitam serviços ao kernel
- [[Interrupção]] — mecanismo de exceção que torna context switch possível
- [[Proteção de Memória x86]] — modo usuário vs. kernel; acesso ao espaço do kernel
- [[Shell]] — exemplo de processo que usa fork/exec/wait
- [[Concorrência e Paralelismo]] — multicore, hyperthreading e ILP
