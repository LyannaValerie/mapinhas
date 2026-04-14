---
title: Sistemas de Representação
aliases:
  - unsigned
  - signed
  - sem sinal
  - sinalizado
  - ponto flutuante
  - floating point
  - IEEE 754
  - precisão simples
  - precisão dupla
  - mantissa
  - expoente binário
tags:
  - computação/fundamentos
date: 2026-04-06
---

# Sistemas de Representação

## Visão geral

Números binários podem ser interpretados de formas diferentes dependendo do **sistema de representação** adotado. Os três principais são:

| Sistema                  | O que representa                                    | Faixa (n bits)      |
| ------------------------ | --------------------------------------------------- | ------------------- |
| **Unsigned** (sem sinal) | Inteiros não negativos                              | 0 a 2ⁿ − 1          |
| **Signed** (sinalizado)  | Inteiros com sinal                                  | −2ⁿ⁻¹ a +2ⁿ⁻¹ − 1   |
| **Ponto flutuante**      | Reais (inteiros + frações + muito grandes/pequenos) | Ver tabela IEEE 754 |

---

## Unsigned (sem sinal / ordinal)

Representação padrão — já coberta em [[Palavras Binárias]]. Todos os bits representam magnitude:

- **Faixa:** 0 a 2ⁿ − 1
- 8 bits → 0 a 255
- 16 bits → 0 a 65.535
- 32 bits → 0 a 4.294.967.295

---

## Signed (sinalizado)

O **bit mais significativo (msb)** é reservado para indicar o **sinal**:
- `0` = positivo
- `1` = negativo

Os demais n − 1 bits representam a magnitude.

**Faixa:** −2ⁿ⁻¹ a +2ⁿ⁻¹ − 1

| Bits | Faixa signed |
|---|---|
| 8 bits | −128 a +127 |
| 16 bits | −32.768 a +32.767 |
| 32 bits | −2.147.483.648 a +2.147.483.647 |
| 64 bits | −9,2 × 10¹⁸ a +9,2 × 10¹⁸ |

> [!note] Assimetria
> O negativo tem um valor a mais que o positivo (ex: −128 mas apenas +127 para 8 bits) porque o zero ocupa uma posição no lado positivo.

---

## Ponto Flutuante (IEEE 754)

Números reais podem ser muito grandes, muito pequenos ou ter casas decimais — o que torna impraticável uma representação por magnitude simples. A solução é a **notação científica binária** (equivalente ao formato científico decimal, ex: −4,6 × 10¹²).

### Estrutura

Um número em ponto flutuante é dividido em três campos:

```
[ Sinal | Expoente | Mantissa ]
```

| Campo | Função |
|---|---|
| **Sinal** | 0 = positivo; 1 = negativo (do número inteiro) |
| **Expoente** | Potência de 2 pela qual a mantissa é multiplicada |
| **Mantissa** | Dígitos significativos do número (parte após "1,") |

A base é sempre **2** — não precisa ser armazenada.

### Formatos IEEE 754

| Formato | Total | Sinal | Expoente | Mantissa | Constante do expoente |
|---|---|---|---|---|---|
| **Precisão Simples** | 32 bits | 1 bit | 8 bits | 23 bits | 127 |
| **Precisão Dupla** | 64 bits | 1 bit | 11 bits | 52 bits | 1.023 |
| **Precisão Dupla Estendida** | 80 bits | 1 bit | 15 bits | 63 bits + 1 bit inteiro | 16.383 |

### Faixas representáveis

| Formato | Precisão | Faixa binária | Faixa decimal |
|---|---|---|---|
| Simples (32 bits) | 24 bits | 2⁻¹²⁶ a 2¹²⁷ | 1,18 × 10⁻³⁸ a 3,40 × 10³⁸ |
| Dupla (64 bits) | 53 bits | 2⁻¹⁰²² a 2¹⁰²³ | 2,23 × 10⁻³⁰⁸ a 1,79 × 10³⁰⁸ |
| Dupla estendida (80 bits) | 64 bits | 2⁻¹⁶³⁸² a 2¹⁶³⁸³ | 3,37 × 10⁻⁴⁹³² a 1,18 × 10⁴⁹³² |

### Notação por excesso (bias do expoente)

O campo de expoente não tem bit de sinal próprio. Para representar expoentes negativos, usa-se **notação por excesso**: soma-se uma constante ao expoente real, garantindo que o valor armazenado seja sempre positivo.

$$\text{expoente armazenado} = \text{expoente real} + \text{constante}$$

---

## Exemplo de conversão — 178,125 para IEEE 754 precisão simples

### Passo 1 — Converter para binário

**Parte inteira:** 178 = `10110010`

**Parte fracionária:** 0,125

| Operação | Resultado | Bit gerado |
|---|---|---|
| 0,125 × 2 | 0,25 | **0** |
| 0,25 × 2 | 0,50 | **0** |
| 0,50 × 2 | 1,00 | **1** → fim |

0,125 = `.001`

**Número completo:** `10110010,001`

### Passo 2 — Normalizar

Mover a vírgula até que o número comece com `1,`:

$$10110010{,}001 = 1{,}0110010001 \times 2^7$$

A vírgula andou **7 casas à esquerda** → expoente = **+7** (positivo quando vírgula vai à esquerda; negativo quando vai à direita).

### Passo 3 — Preencher os campos

**Mantissa:** `0110010001` (os bits após `1,`), completando com zeros à direita até 23 bits:
```
01100100010000000000000
```
> [!note] O `1,` inicial é implícito e **não** armazenado nos formatos simples e dupla. Apenas na precisão dupla estendida o bit inteiro (`1`) é explícito.

**Expoente:** 7 + 127 (constante) = **134** = `10000110`

**Sinal:** `0` (número positivo)

### Resultado final (32 bits)

```
0  10000110  01100100010000000000000
↑  ────┬───  ──────────┬────────────
sinal  exp.           mantissa
```

`01000011001100100010000000000000`

---

## Ver também
- [[Dados Binários]] — sistema binário, msb/lsb
- [[Palavras Binárias]] — nibble, byte, word; endianness
- [[ULA]] — a ULA realiza operações tanto com inteiros quanto com ponto flutuante
