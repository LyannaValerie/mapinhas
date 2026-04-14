---
title: Sinais
aliases:
  - signal
  - signals
  - sinal Unix
  - sinais Unix
  - signal handler
  - pending signal
  - blocked signal
  - signal mask
  - SIGINT
  - SIGKILL
  - SIGCHLD
  - SIGSEGV
  - SIGTERM
  - SIGSTOP
  - SIGCONT
  - sigprocmask
  - sigsuspend
tags:
  - computação/arquitetura
date: 2026-04-14
---

# Sinais

## Definição
Pequenas mensagens que o kernel ou processos enviam a um processo para notificá-lo de algum **evento assíncrono** no sistema. Cada sinal é identificado por um número inteiro positivo e um nome (ex: `SIGINT` = 2).

Sinais são [[Fluxo de Controle Excepcional|ECF]] em nível de aplicação — o processo receptor tem sua execução interrompida para tratar o evento.

## Relações (SPO)
- Kernel → envia sinal a → processo (quando detecta evento do sistema)
- Processo → envia sinal a → outro processo (via `kill()`)
- Sinal → transfere controle para → signal handler no receptor
- Signal mask → bloqueia → entrega de sinais específicos
- Sinal pendente → recebido no máximo → uma vez

---

## Sinais comuns Linux x86-64

| Número | Nome | Causa padrão |
|---|---|---|
| 1 | `SIGHUP` | Terminal encerrado |
| 2 | `SIGINT` | Ctrl+C no terminal |
| 3 | `SIGQUIT` | Ctrl+\\ no terminal |
| 4 | `SIGILL` | Instrução ilegal |
| 5 | `SIGTRAP` | Breakpoint de debug |
| 6 | `SIGABRT` | `abort()` |
| 8 | `SIGFPE` | Divisão por zero / erro FP |
| 9 | `SIGKILL` | Término forçado (não pode ser capturado nem ignorado) |
| 11 | `SIGSEGV` | Referência inválida à memória |
| 14 | `SIGALRM` | Timer `alarm()` expirou |
| 15 | `SIGTERM` | Término gracioso (padrão de `kill`) |
| 17 | `SIGCHLD` | Filho encerrou ou parou |
| 18 | `SIGCONT` | Continuar processo parado |
| 19 | `SIGSTOP` | Parar processo (não pode ser capturado nem ignorado) |
| 20 | `SIGTSTP` | Ctrl+Z — parar interativamente |

---

## Terminologia

**Enviar (send/deliver):** kernel atualiza algum estado no contexto do processo destino. Causas:
1. Kernel detectou evento do sistema (divisão por zero, filho encerrou)
2. Processo invocou `kill()` para pedir envio explícito

**Receber (receive):** processo é forçado pelo kernel a reagir ao sinal. Reações possíveis:
- **Ignorar** o sinal
- **Encerrar** (possivelmente com core dump)
- **Capturar** — executar *signal handler* definido pelo usuário

**Pendente (pending):** sinal enviado mas ainda não recebido. O kernel mantém um **bit vector de pendentes** — bit k setado quando sinal k é enviado, zerado quando recebido. **Sinal pendente é recebido no máximo uma vez** — se chegar outro do mesmo tipo enquanto o primeiro está pendente, ele é descartado.

**Bloqueado (blocked):** sinal cujo recebimento está temporariamente suspenso. O kernel mantém um **bit vector de bloqueados** (signal mask). Sinal bloqueado pode ser enviado (e fica pendente), mas não será recebido até ser desbloqueado.

---

## Enviando sinais

### Via terminal
- **Ctrl+C**: kernel envia `SIGINT` a todos os processos do grupo em foreground
- **Ctrl+Z**: kernel envia `SIGTSTP` ao grupo foreground (suspende)
- **Ctrl+\\**: kernel envia `SIGQUIT` ao grupo foreground

### Via linha de comando
```sh
/bin/kill -9 15213       # envia SIGKILL ao processo 15213
/bin/kill -9 -15213      # envia SIGKILL ao grupo de processos 15213
```

### Via `kill()` em C
```c
#include <sys/types.h>
#include <signal.h>
int kill(pid_t pid, int sig);
// pid > 0: envia ao processo pid
// pid = 0: envia a todos do mesmo grupo
// pid < 0: envia ao grupo |pid|
// Retorna: 0 se OK; -1 em erro
```

### Via `alarm()` — timer interno
```c
#include <unistd.h>
unsigned int alarm(unsigned int secs);
// Cancela alarm anterior; agenda SIGALRM para daqui a secs segundos
// Retorna: segundos restantes do alarm anterior (0 se não havia)
```

---

## Recebendo e capturando sinais

Quando o kernel retorna de uma [[Interrupção|exceção]] ou syscall para modo usuário, verifica se há sinais pendentes e não bloqueados. Se houver, força o processo a receber o sinal.

**Instalando um handler:**
```c
#include <signal.h>
typedef void (*sighandler_t)(int);
sighandler_t signal(int signum, sighandler_t handler);
// handler = SIG_IGN: ignorar
// handler = SIG_DFL: restaurar comportamento padrão
// handler = endereço de função: instalar handler customizado
```

**Fluxo de captura:**
```
Programa executando instrução I_curr
    ↓ sinal k chega
Kernel desvia para handler(k)
Handler executa
    ↓ handler retorna
Programa retoma na instrução seguinte a I_curr
```

O handler é chamado de forma **assíncrona** — pode interromper qualquer instrução do programa.

---

## Bloqueando sinais — `sigprocmask`

```c
#include <signal.h>
int sigprocmask(int how, const sigset_t *set, sigset_t *oldset);
// how = SIG_BLOCK:   adiciona set à mask atual
// how = SIG_UNBLOCK: remove set da mask atual
// how = SIG_SETMASK: substitui mask por set
// oldset: salva mask anterior (pode ser NULL)
```

Funções auxiliares para manipular `sigset_t`:
```c
sigemptyset(&mask);          // zera todos os bits
sigfillset(&mask);           // seta todos os bits
sigaddset(&mask, SIGCHLD);   // adiciona sinal específico
sigdelset(&mask, SIGCHLD);   // remove sinal específico
sigismember(&mask, SIGCHLD); // verifica se está no set
```

**Padrão seguro — bloquear antes de checar flag:**
```c
sigprocmask(SIG_BLOCK, &mask, &prev);
// ... seção crítica ...
sigprocmask(SIG_SETMASK, &prev, NULL);
```

---

## Aguardando sinal — `sigsuspend`

```c
#include <signal.h>
int sigsuspend(const sigset_t *mask);
// Substitui mask temporariamente, suspende até sinal chegar,
// restaura mask original. Equivalente atômico de:
//   sigprocmask(SIG_SETMASK, &mask, &prev);
//   pause();
//   sigprocmask(SIG_SETMASK, &prev, NULL);
```

Usado para aguardar sinal específico sem race condition (versão com `pause()` tem race entre desbloqueio e pause).

---

## Regras para handlers seguros

Handlers executam de forma assíncrona, concorrentemente com o programa principal. Regras:

1. **Manter handler simples** — fazer o mínimo possível
2. **Chamar apenas funções async-signal-safe** — funções reentrantes ou não-interrompíveis (ex: `write`, `_exit`, `kill`, `getpid`; **não** `printf`, `malloc`, `exit`)
3. **Salvar e restaurar `errno`** — handler pode alterar `errno`; salvar no início, restaurar no fim
4. **Bloquear sinais ao acessar dados compartilhados** — previne race conditions
5. **Declarar flags globais como `volatile sig_atomic_t`** — garante leitura/escrita atômica
6. **`sig_atomic_t` não garante atomicidade em operações compostas** — usar `sigprocmask` para proteger regiões críticas maiores

---

## Grupos de processos

Todo processo pertence a exatamente um **grupo de processos**, identificado pelo PGID. Sinais podem ser enviados a grupos inteiros.

```c
pid_t getpgrp(void);              // PGID do processo atual
int setpgid(pid_t pid, pid_t pgid); // muda grupo do processo
```

---

## Ver também
- [[Fluxo de Controle Excepcional]] — sinais como ECF em nível aplicação
- [[Processo]] — estados do processo; fork/waitpid; zombie e SIGCHLD
- [[Interrupção]] — mecanismo de exceção que entrega sinais
- [[Chamada do Sistema]] — kill(), sigprocmask() são syscalls
- [[Shell]] — shell usa sinais para controle de jobs (Ctrl+C, Ctrl+Z)
- [[Concorrência e Paralelismo]] — handlers como exemplo de concorrência
