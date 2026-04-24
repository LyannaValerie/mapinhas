---
title: MIPS — Formato de Instrução
tags:
  - arquitetura/MIPS
aliases:
  - formato instrução MIPS
  - R-type I-type J-type
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# MIPS — Formato de Instrução

Toda instrução MIPS tem **32 bits**. Três formatos; os primeiros 6 bits (op) indicam qual.

## R-type (Register)

```
 31     26 25   21 20   16 15   11 10    6 5      0
+--------+-------+-------+-------+-------+--------+
|   op   |  rs   |  rt   |  rd   | shamt |  funct |
|  6 bits| 5 bits| 5 bits| 5 bits| 5 bits|  6 bits|
+--------+-------+-------+-------+-------+--------+
```

| Campo | Bits | Significado |
|---|---|---|
| `op` | 6 | opcode (sempre 0 para R-type aritmético) |
| `rs` | 5 | primeiro registrador fonte |
| `rt` | 5 | segundo registrador fonte |
| `rd` | 5 | registrador destino |
| `shamt` | 5 | shift amount (0 se não for shift) |
| `funct` | 6 | código da operação específica |

**Exemplos de funct:** `add`=32, `sub`=34, `and`=36, `or`=37, `nor`=39, `slt`=42, `sll`=0, `srl`=2

## I-type (Immediate)

```
 31     26 25   21 20   16 15                    0
+--------+-------+-------+------------------------+
|   op   |  rs   |  rt   |      imediato          |
|  6 bits| 5 bits| 5 bits|        16 bits         |
+--------+-------+-------+------------------------+
```

| Campo | Bits | Significado |
|---|---|---|
| `op` | 6 | opcode identifica a instrução |
| `rs` | 5 | registrador base ou fonte |
| `rt` | 5 | registrador destino (lw) ou fonte (sw) |
| `imm` | 16 | constante com sinal (extensão de sinal para 32 bits) |

**Opcodes:** `addi`=8, `lw`=35, `sw`=43, `beq`=4, `bne`=5, `slti`=10, `lui`=15

> [!note] Reutilização dos campos
> R-type e I-type compartilham os mesmos bits para `op`, `rs`, `rt` — reduz complexidade do hardware de decodificação.

## J-type (Jump)

```
 31     26 25                                    0
+--------+-----------------------------------------+
|   op   |            endereço (26 bits)           |
|  6 bits|                 26 bits                 |
+--------+-----------------------------------------+
```

Endereço efetivo = `(PC+4)[31:28] || imm26 || 00` (concatenação).

**Opcodes:** `j`=2, `jal`=3

## Encoding de Desvio (beq/bne)

O campo de 16 bits em beq/bne é **PC-relativo**:
$$\text{PC alvo} = (\text{PC} + 4) + (\text{imm16} \times 4)$$

Permite alcançar ±$2^{15}$ words (~±128 KB) a partir da instrução seguinte.

## Tabela Resumo de Encodings

| Instrução | Formato | op | funct |
|---|---|---|---|
| `add` | R | 0 | 32 |
| `sub` | R | 0 | 34 |
| `slt` | R | 0 | 42 |
| `addi` | I | 8 | — |
| `lw` | I | 35 | — |
| `sw` | I | 43 | — |
| `beq` | I | 4 | — |
| `bne` | I | 5 | — |
| `j` | J | 2 | — |
| `jal` | J | 3 | — |

## Ver também

- [[MIPS — ISA]]
- [[MIPS — Conjunto de Instruções]]
- [[MIPS — Modos de Endereçamento]]
- [[Formatos de Instrução]]
- [[Linguagem de Máquina]]
