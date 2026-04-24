---
title: Conversão de Bases
aliases:
  - base conversion
  - conversão binário decimal
  - conversão decimal binário
  - divisões sucessivas
  - ponto binário
  - binário para octal
  - binário para hexadecimal
  - octal para binário
tags:
  - computação/fundamentos
date: 2026-04-13
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Fundamentos]]

# Conversão de Bases

## Definição

Procedimentos para traduzir números entre os sistemas de numeração usados em computação: **binário (2)**, **octal (8)**, **decimal (10)** e **hexadecimal (16)**.

## Relações (SPO)
- Conversão binário→octal → agrupa → 3 bits por dígito octal
- Conversão binário→hexadecimal → agrupa → 4 bits por dígito hex
- Divisões sucessivas → convertem → decimal inteiro para binário
- Multiplicações sucessivas → convertem → binário para decimal

---

## Binário ↔ Octal

Cada dígito octal corresponde a exatamente **3 bits**. Converter é apenas agrupar.

### Binário → Octal
1. Agrupar bits de 3 em 3 a partir do ponto binário (preencher com zeros se necessário)
2. Substituir cada grupo pelo dígito octal equivalente (0–7)

```
0 001 100 101 001 000 . 101 011 011 00
  1   4   5   1   0  .  5   5   4
→ 14510.554 (octal)
```

### Octal → Binário
Substituir cada dígito pelos 3 bits equivalentes.

---

## Binário ↔ Hexadecimal

Cada dígito hexadecimal corresponde a exatamente **4 bits**.

### Binário → Hexadecimal
Agrupar bits de 4 em 4 a partir do ponto binário.

```
0001 1001 0100 1000 . 1011 0110 0
   1    9    4    8 .    B    6
→ 1948.B6 (hex)
```

### Hexadecimal → Binário
Substituir cada dígito pelos 4 bits equivalentes.

---

## Decimal → Binário

### Método — Divisões Sucessivas (inteiros)
1. Dividir por 2; registrar o **resto** (0 ou 1)
2. Repetir com o quociente até chegar a 0
3. Ler os restos de **baixo para cima**

```
1492 ÷ 2 = 746  r=0
 746 ÷ 2 = 373  r=0
 373 ÷ 2 = 186  r=1
 186 ÷ 2 =  93  r=0
  93 ÷ 2 =  46  r=1
  46 ÷ 2 =  23  r=0
  23 ÷ 2 =  11  r=1
  11 ÷ 2 =   5  r=1
   5 ÷ 2 =   2  r=1
   2 ÷ 2 =   1  r=0
   1 ÷ 2 =   0  r=1
                   ↑ leia daqui para cima

1492₁₀ = 10111010100₂
```

---

## Binário → Decimal

### Método 1 — Soma de Potências
Somar 2ⁿ para cada bit 1 na posição n (da direita, começando em 0).

`10110₂ = 2⁴ + 2² + 2¹ = 16 + 4 + 2 = 22₁₀`

### Método 2 — Multiplicações Sucessivas (Horner)
Escrever o número verticalmente, bit mais significativo **no fundo**. Começar com o valor 1 na linha de baixo. Cada linha n = 2 × linha n−1 + bit da linha n.

```
Número: 101110110111

  1    →  1
  1    →  1 + 2×1    = 3
  1    →  1 + 2×3    = 7
  0    →  0 + 2×7    = 14
  1    →  1 + 2×14   = 29
  1    →  1 + 2×29   = 59
  0    →  0 + 2×59   = 118 (continua...)

resultado final: 2999
```

---

## Tabela de referência (0–15)

| Decimal | Binário | Octal | Hexadecimal |
|---|---|---|---|
| 0 | 0000 | 0 | 0 |
| 1 | 0001 | 1 | 1 |
| 2 | 0010 | 2 | 2 |
| 3 | 0011 | 3 | 3 |
| 4 | 0100 | 4 | 4 |
| 5 | 0101 | 5 | 5 |
| 6 | 0110 | 6 | 6 |
| 7 | 0111 | 7 | 7 |
| 8 | 1000 | 10 | 8 |
| 9 | 1001 | 11 | 9 |
| 10 | 1010 | 12 | A |
| 11 | 1011 | 13 | B |
| 12 | 1100 | 14 | C |
| 13 | 1101 | 15 | D |
| 14 | 1110 | 16 | E |
| 15 | 1111 | 17 | F |

---

## Ver também
- [[Dados Binários]] — sistema binário, representação posicional
- [[Hexadecimal]] — base 16, notações (0x, h)
- [[Sistemas de Representação]] — signed, unsigned, ponto flutuante
