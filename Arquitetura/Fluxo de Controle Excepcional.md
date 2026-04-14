---
title: Fluxo de Controle Excepcional
aliases:
  - ECF
  - Exceptional Control Flow
  - fluxo de controle excepcional
  - fluxo excepcional
tags:
  - computação/arquitetura
date: 2026-04-14
---

# Fluxo de Controle Excepcional

## Definição
Mudanças abruptas no fluxo de controle que sistemas usam para reagir a **mudanças no estado do sistema** não capturadas por variáveis internas do programa.

O fluxo normal é uma sequência suave: instruções Ik e Ik+1 são adjacentes na memória. Mudanças abruptas causadas por jumps, calls e returns reagem ao **estado do programa**. ECF reage ao **estado do sistema** — eventos externos ou internos que o programa não controla.

## Relações (SPO)
- ECF → ocorre em → todos os níveis do sistema computacional
- ECF nível hardware → [[Interrupção|exceção]] dispara → exception handler
- ECF nível SO → kernel realiza → [[Processo#Troca de contexto (Context Switching)|troca de contexto]]
- ECF nível aplicação → processo envia → sinal para outro processo
- ECF nível programa → `setjmp`/`longjmp` realiza → salto não-local
- ECF → implementa → E/S, processos, [[Memória Virtual]], concorrência

---

## Níveis de ECF

| Nível | Mecanismo | Exemplo |
|---|---|---|
| Hardware | [[Interrupção]] → handler | Page fault, divisão por zero, timer |
| SO | [[Processo#Troca de contexto (Context Switching)\|Troca de contexto]] | Kernel transfere CPU entre processos via context switch |
| Aplicação | Sinal | Processo envia `SIGCHLD`, `SIGINT`, `SIGSEGV` |
| Programa | Salto não-local | `setjmp`/`longjmp` em C; `try/catch/throw` em C++/Java |

---

## Por que ECF importa

- **Conceitos do sistema**: ECF é o mecanismo base que o SO usa para implementar E/S, processos e [[Memória Virtual]] — entender ECF é pré-requisito para entender esses conceitos
- **Interação com o SO**: [[Chamada do Sistema]] é uma forma de ECF (*trap*) — o programa invoca o kernel via mecanismo de exceção controlada
- **Novos programas**: shells Unix, servidores web e programas concorrentes dependem dos mecanismos de ECF do SO (criar processos, aguardar término, detectar eventos)
- **Concorrência**: ECF é o mecanismo base da concorrência — handlers interrompem programas, processos se alternam, sinais interrompem execução
- **Exceções de software**: `try/catch/throw` em C++ e Java são ECF em nível de aplicação; `setjmp`/`longjmp` em C são a versão de baixo nível — entender ECF revela como essas construções funcionam internamente

---

## Sinais (ECF nível aplicação)

[[Sinais|Sinais Unix]] permitem que o kernel ou outros processos notifiquem um processo de eventos assíncronos. O processo interrompido executa um **signal handler** — função instalada pelo usuário — e depois retoma a execução normal.

Exemplos: `SIGCHLD` (filho encerrou), `SIGINT` (Ctrl+C), `SIGSEGV` (acesso inválido à memória), `SIGKILL` (término forçado).

---

## Salto não-local (ECF nível programa)

[[Salto Não-local|`setjmp`/`longjmp`]] em C permitem pular diretamente de uma função para outra já em execução, sem seguir a disciplina normal de call/return. ECF em nível de aplicação — base para `try/catch/throw` em C++ e Java.

---

## Ver também
- [[Interrupção]] — exceções como forma de ECF em nível hardware; exception table (IDT); 4 classes de exceção
- [[Processo]] — troca de contexto, fork/exec/wait como ECF em nível SO
- [[Sinais]] — ECF em nível aplicação; signal handlers; pending/blocked
- [[Salto Não-local]] — setjmp/longjmp como ECF em nível programa
- [[Chamada do Sistema]] — trap/syscall como ECF controlado para entrada no kernel
- [[Memória Virtual]] — page fault é ECF que torna memória virtual possível
- [[Concorrência e Paralelismo]] — ECF como base da concorrência em todos os níveis
- [[Shell]] — exemplo de programa que usa ECF (fork, wait, sinais)
