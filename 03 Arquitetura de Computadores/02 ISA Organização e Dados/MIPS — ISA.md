---
title: MIPS — ISA
tags:
  - arquitetura/MIPS
  - arquitetura/ISA
aliases:
  - MIPS
  - MIPS32
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# MIPS — ISA

**MIPS** (Microprocessor without Interlocked Pipeline Stages) — arquitetura [[RISC e CISC|RISC]] desenvolvida em Stanford (~1981). Base dos livros P&H e de processadores embarcados.

## Características Fundamentais

| Propriedade | Valor |
|---|---|
| Largura de palavra | 32 bits |
| Registradores | 32 × 32 bits |
| Endereçamento | byte-addressed |
| Endianness | big-endian (padrão) |
| Tamanho instrução | 32 bits (fixo) |
| Espaço de memória | $2^{30}$ words ($2^{32}$ bytes) |

> [!info] word
> Unidade natural de acesso no MIPS: **32 bits**. Endereços de words sequenciais diferem por 4.

## Quatro Princípios de Design

> [!important] Design Principle 1: Simplicity favors regularity
> Instruções com número fixo de operandos (sempre 3). Hardware regular = hardware simples.

> [!important] Design Principle 2: Smaller is faster
> 32 registradores, não mais. Mais registradores → sinais percorrem distâncias maiores → clock mais lento.

> [!important] Design Principle 3: Make the common case fast
> Constantes dentro da instrução (imediatos). Evita load extra da memória.

> [!important] Design Principle 4: Good design demands good compromises
> Formatos R/I/J distintos para instruções distintas, mas com campos comuns nos bits altos para simplificar o hardware de decodificação.

## Subtópicos

- [[MIPS — Registradores]] — convenções, caller/callee-saved
- [[MIPS — Conjunto de Instruções]] — tabela completa
- [[MIPS — Formato de Instrução]] — R-type, I-type, J-type
- [[MIPS — Convenção de Chamada]] — stack frame, jal/jr
- [[MIPS — Modos de Endereçamento]] — 5 modos

## Relações com o Vault

- [[ISA]] — conceito geral de ISA
- [[RISC e CISC]] — MIPS como RISC canônico
- [[Pipeline]] — MIPS projetado para pipeline eficiente
- [[Programa Armazenado]] — conceito de instrução como número
- [[Survey ISAs RISC (P&H Ap. K)]] — comparativo com outras ISAs RISC
- [[Formatos de Instrução]] — detalhe sobre encoding
- [[Modos de Endereçamento]] — modos gerais
