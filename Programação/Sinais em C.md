---
title: Sinais em C
aliases:
  - signal.h
  - signal C
  - SIGINT C
  - handler de sinal C
tags:
  - computação/programação/c
date: 2026-04-21
---

# Sinais em C

## Definição

`<signal.h>` fornece mecanismo para lidar com condições excepcionais durante execução: interrupções externas, erros aritméticos, violações de acesso. Opera no nível da linguagem C, distinto da interface de sinais POSIX de nível OS.

Fonte: K&R Appendix B, §B.9.

## Relações (SPO)

| Sujeito | Predicado | Objeto |
|---|---|---|
| `signal()` | registra | handler para sinal específico |
| `raise()` | envia | sinal ao próprio processo |
| `SIG_DFL` | indica | comportamento padrão da implementação |
| `SIG_IGN` | indica | sinal ignorado |
| handler | é redefinido para | `SIG_DFL` quando sinal ocorre (antes de chamar o handler) |

## Sinais padrão

| Constante | Condição |
|---|---|
| `SIGABRT` | terminação anormal (ex: `abort()`) |
| `SIGFPE` | erro aritmético (divisão por zero, overflow) |
| `SIGILL` | instrução ilegal |
| `SIGINT` | atenção interativa (ex: Ctrl+C) |
| `SIGSEGV` | acesso ilegal à memória |
| `SIGTERM` | pedido de terminação |

## Interface

```c
void (*signal(int sig, void (*handler)(int)))(int)
/* define handler para sig; retorna handler anterior ou SIG_ERR em erro */

int raise(int sig)
/* envia sinal sig ao próprio programa; retorna não-zero se falhar */
```

### Handlers especiais

```c
signal(SIGINT, SIG_DFL);   /* restaura comportamento padrão */
signal(SIGINT, SIG_IGN);   /* ignora SIGINT */
```

## Exemplo

```c
#include <signal.h>
#include <stdio.h>

static volatile sig_atomic_t got_interrupt = 0;

void handle_int(int sig) {
    got_interrupt = 1;
    /* re-registrar handler (comportamento padrão após disparo) */
    signal(SIGINT, handle_int);
}

int main(void) {
    signal(SIGINT, handle_int);
    while (!got_interrupt) {
        /* loop principal */
    }
    printf("Interrompido.\n");
    return 0;
}
```

> [!warning] Restrições dentro de handlers
> O handler deve fazer o mínimo possível. Apenas `sig_atomic_t` é garantidamente seguro para leitura/escrita. Não chamar funções não-reentrantes (ex: `printf`, `malloc`) dentro de handlers ANSI C.

> [!info] `volatile sig_atomic_t`
> Tipo inteiro garantidamente acessível atomicamente. Usar com `volatile` para variáveis compartilhadas entre código normal e handler de sinal.

## Diferença: ANSI C vs POSIX

Esta nota cobre a interface ANSI C (`signal.h` padrão). POSIX expande com `sigaction()`, máscaras de sinais, sinais em tempo real — ver [[Sinais]] para a visão de nível OS.

## Ver também

- [[Sinais]] — sinais a nível de sistema operacional (POSIX/UNIX)
- [[Qualificadores de Tipo em C]] — `volatile sig_atomic_t`
- [[Biblioteca stdlib.h em C]] — `abort()` dispara `SIGABRT`
- [[Listas de Argumentos Variáveis em C]] — `<stdarg.h>` companheiro de `<signal.h>`
