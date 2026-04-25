---
title: Operações Bit a Bit
aliases:
  - bitwise
  - operações bitwise
  - operações lógicas binárias
  - AND bit a bit
  - OR bit a bit
  - XOR bit a bit
  - NOT bit a bit
tags:
  - computação/fundamentos
  - computação/arquitetura
date: 2026-04-24
source: "desconhecida"
---

%% criado pelo Codex %%
%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Fundamentos]]

# Operações Bit a Bit

## Definição
Operações que manipulam bits individualmente dentro de uma palavra binária. São aplicadas posição por posição e retornam outro padrão de bits.

## Relações (SPO)
- Operações Bit a Bit → operam sobre → [[Dados Binários]]
- Operações Bit a Bit → implementam → [[Álgebra Booleana]]
- Operações Bit a Bit → manipulam → [[Flags Bit a Bit]]
- Deslocamento à esquerda → equivale a → multiplicação por potência de 2 em inteiros sem overflow

## Operadores básicos

| Operador | Regra |
|---|---|
| AND | retorna `1` só quando ambos os bits são `1` |
| OR | retorna `1` quando ao menos um bit é `1` |
| XOR | retorna `1` quando os bits são diferentes |
| NOT | inverte cada bit |

## Operações fundamentais

### AND (`&`)
Bit 1 somente se **ambos** forem 1. Uso principal: **mascarar/isolar bits**.

```
  1010 1010
& 0000 1111
-----------
  0000 1010
```

### OR (`|`)
Bit 1 se **qualquer** for 1. Uso principal: **setar bits**.

```
  1010 0000
| 0000 1111
-----------
  1010 1111
```

### XOR (`^`)
Bit 1 se os dois **diferirem**. Uso: **inverter bits específicos**, comparar.

```
  1010 1010
^ 0000 1111
-----------
  1010 0101
```

### NOT (`~`)
**Inverte** todos os bits (complemento a 1).

```
~ 1010 0101
-----------
  0101 1010
```

### Deslocamento esquerdo `<<` (SHL)
Desloca bits para a esquerda; posições direitas tornam-se 0. Equivale a **×2ⁿ**.

```
0000 0001 << 3 = 0000 1000   (1 × 8 = 8)
```

### Deslocamento direito `>>` (SHR)
Desloca bits para a direita. Equivale a **÷2ⁿ** (inteiro sem sinal).

```
0001 0000 >> 2 = 0000 0100   (16 ÷ 4 = 4)
```

> [!warning] SHR lógico vs aritmético
> Em inteiros **com sinal**, `>>` pode ser lógico (preenche com 0) ou aritmético (preserva bit de sinal). Em C, o comportamento de `>>` em negativos é _implementation-defined_.

## Idiomas comuns em C

| Operação | Código |
|---|---|
| Testar bit n | `(x >> n) & 1` |
| Setar bit n | `x \| (1 << n)` |
| Zerar bit n | `x & ~(1 << n)` |
| Inverter bit n | `x ^ (1 << n)` |
| Máscara dos n bits inferiores | `x & ((1 << n) - 1)` |

## Uso em detecção de processador (x86)

Técnica usada pelo BIOS para identificar família de CPU: empilha valor 0 no registrador de flags (POPF), depois lê de volta (PUSHF + POP AX). Nas famílias 8086/8088/80188, bits 12–15 do registrador de flags ficam sempre em 1 — o 80286 e 80386 não fazem isso:

```asm
xor  ax, ax
push ax
popf                  ; carrega 0 no registrador de flags
pushf
pop  ax
and  ax, 0F000h       ; isola bits 12-15
cmp  ax, 0F000h       ; se todos = 1 → não é 286/386
```

## Ver também
- [[Flags Bit a Bit]]
- [[Dados Binários]]
- [[Palavras Binárias]]
- [[Sistemas de Representação]]

