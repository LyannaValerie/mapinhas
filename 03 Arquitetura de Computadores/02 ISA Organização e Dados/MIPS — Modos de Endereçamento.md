---
title: MIPS — Modos de Endereçamento
tags:
  - arquitetura/MIPS
aliases:
  - addressing modes MIPS
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# MIPS — Modos de Endereçamento

MIPS usa **5 modos** de endereçamento.

## 1. Imediato (Immediate)

Operando está na própria instrução (campo de 16 bits com extensão de sinal).

```mips
addi $s1,$s2,100    # operando = 100
```

Alcance: −32768 a +32767.

## 2. Registrador (Register)

Operando está em um dos 32 registradores.

```mips
add $s1,$s2,$s3     # operandos = conteúdo de $s2 e $s3
```

## 3. Base + Deslocamento (Base/Displacement)

Endereço = `registrador_base + offset_16bits`. Usado em `lw`, `sw`, etc.

```mips
lw $s1,100($s2)     # Mem[$s2 + 100]
```

O registrador (`$s2`) é chamado de **index register** historicamente.

## 4. PC-relativo (PC-relative)

Usado em desvios condicionais. Endereço calculado a partir do PC da **instrução seguinte**:

$$\text{PC alvo} = (\text{PC} + 4) + (\text{imm16} \times 4)$$

```mips
beq $s0,$s1,L       # salta ±2^15 words a partir de PC+4
```

> [!note] Por que relativo ao PC+4?
> O PC já foi incrementado para a instrução seguinte quando o cálculo ocorre.

## 5. Pseudodireto (Pseudodirect)

Usado em `j` e `jal`. Endereço de 32 bits montado por concatenação:

$$\text{PC alvo} = \text{PC+4}_{[31:28]} \mathbin{||} \text{imm26} \mathbin{||} \text{00}$$

```mips
j 10000             # salta para 10000 × 4 = 40000
```

Alcance: 256 MB dentro do mesmo segmento de 256 MB do PC atual.

> [!warning] Limitação do J-type
> Se o destino estiver em outro segmento de 256 MB, o assembler substitui por `jr` com registrador carregado por `lui`+`ori`.

## Resumo

| Modo | Instrução exemplo | Cálculo do operando/endereço |
|---|---|---|
| Imediato | `addi $t0,$t1,5` | campo imm na instrução |
| Registrador | `add $t0,$t1,$t2` | conteúdo do registrador |
| Base+Offset | `lw $t0,8($t1)` | `Mem[$t1 + 8]` |
| PC-relativo | `beq $s0,$s1,L` | `(PC+4) + imm16×4` |
| Pseudodireto | `j Label` | `PC+4[31:28] || imm26 || 00` |

## Ver também

- [[MIPS — ISA]]
- [[MIPS — Formato de Instrução]]
- [[MIPS — Conjunto de Instruções]]
- [[Modos de Endereçamento]]
