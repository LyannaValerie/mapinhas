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

## Ver também
- [[Registrador]]
- [[Registradores x86]] — EFLAGS/RFLAGS com mapa completo de bits
- [[Processador]]
- [[Linguagem Assembly]] — instruções de desvio condicional consultam flags
- [[Sistemas de Representação]] — representação de números negativos
- [[Transmissão de Dados]] — flag de paridade e detecção de erros
