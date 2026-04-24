---
title: Flag
aliases:
  - flags
  - registrador de flags
  - registrador de status
  - status register
  - overflow flag
  - sign flag
  - zero flag
  - carry flag
  - parity flag
tags:
  - computação/arquitetura
date: 2026-04-09
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Flag

## Definição
Registrador especial de **apenas um bit** que indica que uma situação particular ocorreu durante uma operação do [[Processador]]. Quando o bit está em "1", a flag está **ativada**.

Como cada flag ocupa um único bit, o processador agrupa todas as flags em um **único registrador** compartilhado. Exemplo: um registrador de 16 bits pode acomodar até 16 flags distintas.

## Relações (SPO)
- Flag → é → [[Registrador]] de 1 bit
- Flag → sinaliza → condição ou resultado de operação aritmética/lógica
- Flag → consultada por → instruções de desvio condicional (JZ, JNZ, JL…)
- Registrador de flags → agrupa → todas as flags em um único registrador

## Flags comuns

| Flag | Nome | Ativada quando |
|---|---|---|
| **Overflow** | Transbordamento | Resultado maior do que o registrador de destino consegue armazenar (ex: resultado de 36 bits em registrador de 32 bits) |
| **Sign** | Sinal | Bit mais significativo é usado para indicar sinal — "0" = positivo, "1" = negativo |
| **Zero** | Zero | Resultado da operação é zero |
| **Parity** | Paridade | Bit ajustado para que o total de uns no byte menos significativo do resultado seja par |
| **Carry** | Carry/Empréstimo | Houve "vai um" ou "empréstimo" no último cálculo executado |

### Flag de Sinal (Sign) — detalhe

Quando ativada, o bit mais significativo do registrador passa a indicar o **sinal do número**, reduzindo o intervalo positivo à metade mas permitindo representar negativos:

| Registrador | Sem flag de sinal | Com flag de sinal ativa |
|---|---|---|
| 16 bits | 0 a 65.535 (2¹⁶) | −32.768 a +32.767 |

Ver [[Sistemas de Representação]] para representação de negativos em binário (complemento de dois).

## Flags em x86 — EFLAGS / RFLAGS

Na arquitetura x86, todas as flags estão agrupadas em um único registrador:

| Modo | Registrador | Tamanho |
|---|---|---|
| Modo real (8086/286) | FLAGS | 16 bits |
| Modo protegido 32 bits | EFLAGS | 32 bits |
| Modo de 64 bits | RFLAGS | 64 bits (bits 63–32 reservados) |

Além das flags de status (CF, PF, AF, ZF, SF, OF) há a flag de controle **DF** (Direction Flag — controla direção em instruções de string) e flags de sistema (IF para interrupções, TF para debug single-step, VM para modo Virtual-8086, IOPL para nível de privilégio de E/S etc.).

Ver [[Registradores x86#Registrador de Flags — EFLAGS / RFLAGS]] para o mapa completo de todos os bits.

## Condition Codes x86-64 — Semântica Aritmética

Em x86-64, CF, ZF, SF e OF são atualizados como efeito colateral das instruções aritméticas e lógicas. A instrução **`leaq`** é exceção: nunca altera flags (propositalmente — calcula endereços).

### Como as flags são definidas

Dado `t = a + b` (ou operação equivalente):

| Flag | Condição de ativação |
|---|---|
| **CF** (Carry) | Carry/borrow no bit mais significativo — detecta overflow **sem sinal** |
| **ZF** (Zero) | `t == 0` |
| **SF** (Sign) | `t < 0` (bit mais significativo = 1) |
| **OF** (Overflow) | Overflow em complemento de dois — `(a>0 && b>0 && t<0) \|\| (a<0 && b<0 && t>=0)` |

Para operações lógicas (`and`, `or`, `xor`): CF e OF são zerados; ZF e SF refletem o resultado.

### Instruções que modificam flags sem gravar resultado

| Instrução | Equivale a | Uso típico |
|---|---|---|
| `cmp a, b` | `b - a` (descarta resultado) | Comparação: seta CF/ZF/SF/OF como subtração |
| `test a, b` | `a & b` (descarta resultado) | Verificar bit / checar se registrador é zero (`test %rax, %rax` → ZF=1 se zero) |

### Instruções `SET` — leitura de condição para byte

`SET`_cc_ grava 0 ou 1 num registrador de 1 byte conforme a condição _cc_. Exemplos:

| Instrução | Condição | Flags |
|---|---|---|
| `sete` / `setz` | `==` / zero | ZF |
| `setne` / `setnz` | `!=` / não-zero | ~ZF |
| `sets` | negativo | SF |
| `setg` / `setnle` | `>` (com sinal) | ~(SF^OF) & ~ZF |
| `setl` / `setnge` | `<` (com sinal) | SF^OF |
| `seta` / `setnbe` | `>` (sem sinal) | ~CF & ~ZF |
| `setb` / `setnae` | `<` (sem sinal) | CF |

Para obter inteiro de largura maior: combinar com `movzbl` (zero-extends o byte para 32/64 bits).

---


- [[Registrador]]
- [[Registradores x86]] — EFLAGS/RFLAGS com mapa completo de bits
- [[Processador]]
- [[Linguagem Assembly]] — instruções de desvio condicional consultam flags
- [[Modos de Endereçamento]] — leaq não altera flags
- [[Convenção de Chamada x86-64]] — flags volatile entre chamadas (caller-saved implícito)
- [[Sistemas de Representação]] — representação de números negativos
- [[Transmissão de Dados]] — flag de paridade e detecção de erros
