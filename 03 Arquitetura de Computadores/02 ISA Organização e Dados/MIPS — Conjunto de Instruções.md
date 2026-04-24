---
title: MIPS — Conjunto de Instruções
tags:
  - arquitetura/MIPS
aliases:
  - instruções MIPS
  - MIPS instruction set
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# MIPS — Conjunto de Instruções

## Aritméticas

| Instrução | Exemplo | Operação |
|---|---|---|
| `add` | `add $s1,$s2,$s3` | `$s1 = $s2 + $s3` (detecta overflow) |
| `sub` | `sub $s1,$s2,$s3` | `$s1 = $s2 – $s3` (detecta overflow) |
| `addi` | `addi $s1,$s2,100` | `$s1 = $s2 + 100` |
| `addu` | `addu $s1,$s2,$s3` | sem detecção de overflow |
| `subu` | `subu $s1,$s2,$s3` | sem detecção de overflow |
| `addiu` | `addiu $s1,$s2,100` | sem overflow, extensão de sinal |

> [!note] Sem `subi`
> `addi` com constante negativa equivale a subtract immediate → `subi` é supérfluo.

## Transferência de Dados

| Instrução | Exemplo | Operação |
|---|---|---|
| `lw` | `lw $s1,100($s2)` | `$s1 = Mem[$s2+100]` |
| `sw` | `sw $s1,100($s2)` | `Mem[$s2+100] = $s1` |
| `lb` | `lb $s1,100($s2)` | carrega byte com extensão de sinal |
| `lbu` | `lbu $s1,100($s2)` | carrega byte sem extensão de sinal |
| `sb` | `sb $s1,100($s2)` | armazena byte |
| `lh` | `lh $s1,100($s2)` | carrega halfword (16 bits) |
| `lhu` | `lhu $s1,100($s2)` | carrega halfword sem sinal |
| `sh` | `sh $s1,100($s2)` | armazena halfword |
| `lui` | `lui $s1,100` | `$s1 = 100 << 16` (carrega upper 16 bits) |

> [!tip] Constantes de 32 bits
> `lui $at, upper16` + `ori $at, $at, lower16` → carrega constante 32 bits em dois passos (o assembler faz isso automaticamente).

## Lógicas

| Instrução | Exemplo | Operação |
|---|---|---|
| `and` | `and $s1,$s2,$s3` | `$s1 = $s2 & $s3` |
| `or` | `or $s1,$s2,$s3` | `$s1 = $s2 \| $s3` |
| `nor` | `nor $s1,$s2,$s3` | `$s1 = ~($s2 \| $s3)` |
| `andi` | `andi $s1,$s2,100` | `$s1 = $s2 & 100` |
| `ori` | `ori $s1,$s2,100` | `$s1 = $s2 \| 100` |
| `sll` | `sll $s1,$s2,10` | `$s1 = $s2 << 10` |
| `srl` | `srl $s1,$s2,10` | `$s1 = $s2 >> 10` (lógico) |
| `sra` | `sra $s1,$s2,10` | `$s1 = $s2 >> 10` (aritmético) |

> [!note] NOR como NOT
> `nor $t0,$t1,$zero` ≡ `NOT $t1` — sem instrução NOT separada.

## Comparação

| Instrução | Exemplo | Operação |
|---|---|---|
| `slt` | `slt $s1,$s2,$s3` | `$s1 = ($s2 < $s3) ? 1 : 0` |
| `slti` | `slti $s1,$s2,100` | com imediato |
| `sltu` | `sltu $s1,$s2,$s3` | sem sinal |
| `sltiu` | `sltiu $s1,$s2,100` | sem sinal, com imediato |

## Desvio Condicional

| Instrução | Exemplo | Operação |
|---|---|---|
| `beq` | `beq $s1,$s2,L` | salta se `$s1 == $s2` |
| `bne` | `bne $s1,$s2,L` | salta se `$s1 != $s2` |

Combinados com `slt`: `blt`, `bge`, `bgt`, `ble` são pseudoinstruções.

## Salto Incondicional

| Instrução | Exemplo | Operação |
|---|---|---|
| `j` | `j 2500` | PC = 2500 × 4 (J-type) |
| `jr` | `jr $ra` | PC = `$ra` |
| `jal` | `jal 2500` | `$ra = PC+4`; PC = 2500 × 4 |

## Pseudoinstruções Comuns

| Pseudo | Expansão |
|---|---|
| `move $t0,$t1` | `add $t0,$zero,$t1` |
| `blt $s0,$s1,L` | `slt $at,$s0,$s1` + `bne $at,$zero,L` |
| `li $t0,1000` | `addi $t0,$zero,1000` |

## Ver também

- [[MIPS — ISA]]
- [[MIPS — Formato de Instrução]]
- [[MIPS — Modos de Endereçamento]]
- [[Linguagem de Máquina]]
- [[Linguagem Assembly]]
