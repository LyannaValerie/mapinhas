---
title: Chamada do Sistema
aliases:
  - system call
  - chamada do supervisor
  - macro de sistema operacional
  - syscall
tags:
  - computação/arquitetura
date: 2026-04-06
---

# Chamada do Sistema

## Definição
Instrução do nível 3 ([[Sistema Operacional]]) que não existe no nível [[ISA]]. Permite que programas solicitem serviços ao SO — como operações de entrada/saída, alocação de memória e controle de processos.

## Relações (SPO)
- Chamada do Sistema → pertence ao → nível 3 ([[Sistema Operacional]])
- Chamada do Sistema → não existe em → nível [[ISA]] (nível 2)
- Chamada do Sistema → interpretada por → [[Sistema Operacional]]
- Chamada do Sistema → evoluiu de → cartões de controle (FMS, IBM 709, ~1960)

## Evolução do nome

| Época | Nome |
|---|---|
| ~1960 | Instruções virtuais (cartões `*FORTRAN`, `*DATA`) |
| 1960s–1970s | Macros de sistema operacional / chamadas do supervisor |
| Hoje | **Chamada do sistema** (*system call*) |

## Ver também
- [[Sistema Operacional]]
- [[Níveis de Abstração]]
- [[ISA]]
