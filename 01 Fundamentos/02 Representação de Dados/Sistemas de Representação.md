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
  - complemento de dois
  - complemento de um
  - magnitude com sinal
  - excesso
  - números não normalizados
  - denormalized
  - NaN
  - not a number
  - precisão finita
  - T2U
  - U2T
  - extensão de sinal
  - sign extension
  - zero extension
  - truncamento
  - arredondamento para par
  - round to even
  - arredondamento IEEE
tags:
  - computação/fundamentos
date: 2026-04-06
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Fundamentos]]

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

### Sistemas de representação de negativos

Quatro sistemas foram usados historicamente em computadores:

| Sistema | Como negar | Zeros | Observação |
|---|---|---|---|
| **Magnitude com sinal** | Inverte o bit de sinal | +0 e −0 | Dois zeros; aritmética complexa |
| **Complemento de 1** | Inverte todos os bits | +0 e −0 | Obsoleto; vai-um de contorno |
| **Complemento de 2** | Inverte todos os bits, soma 1 | Apenas +0 | Padrão moderno |
| **Excesso 2^(m−1)** | Armazena N + 2^(m−1) | Um zero | Idêntico ao compl. 2 com bit de sinal invertido |

O **complemento de dois** é usado em praticamente todos os processadores modernos por ter um único zero e aritmética simples.

#### Conversão para complemento de dois

Para negar um número de m bits:
1. Inverter todos os bits (complemento de 1)
2. Somar 1 ao resultado

```
00000110  (+6)
11111001  (passo 1: complemento de 1)
11111010  (passo 2: +1 = −6 em complemento de 2)
```

Se houver vai-um no bit mais à esquerda após a soma, ele é descartado.

> [!note] Por que sempre sobra um padrão de bit "extra"?
> Com m bits existem 2^m padrões (número par). Queremos um único zero e mesma quantidade de positivos e negativos. Isso exigiria 2^m − 1 valores (número ímpar) — impossível. Sempre sobra um padrão. Em complemento de 2, esse padrão é um negativo sem contraparte positiva (ex: −128 em 8 bits).

### Aritmética binária

Tabela de adição binária:

| + | 0 | 1 |
|---|---|---|
| **0** | 0 | 1 |
| **1** | 1 | 0 (vai-um 1) |

**Vai-um de contorno** (complemento de 1): vai-um gerado no bit mais à esquerda é somado ao bit mais à direita.

**Complemento de 2**: vai-um gerado no bit mais à esquerda é descartado.

**Detecção de overflow:** ocorre excesso se, e somente se, o vai-um **para** o bit de sinal for diferente do vai-um **do** bit de sinal. Processadores preservam esse bit em um flag de overflow especial.

---

## Conversões entre Signed e Unsigned

A maioria dos processadores — e C — mantém o padrão de bits idêntico ao converter entre signed e unsigned do mesmo tamanho. O que muda é apenas a **interpretação**.

### T2U — Complemento de dois para unsigned

Para $x$ no intervalo $[TMin_w,\ TMax_w]$:

$$T2U_w(x) = \begin{cases} x + 2^w & \text{se } x < 0 \\ x & \text{se } x \geq 0 \end{cases}$$

O bit de sinal, que valia $-2^{w-1}$, passa a valer $+2^{w-1}$ — diferença de $2^w$.

> [!example] Exemplos (w = 16)
> $T2U_{16}(-12345) = -12345 + 65536 = 53191$  
> $T2U_{32}(-1) = -1 + 2^{32} = 4.294.967.295 = UMax_{32}$

### U2T — Unsigned para complemento de dois

Para $u$ no intervalo $[0,\ UMax_w]$:

$$U2T_w(u) = \begin{cases} u & \text{se } u \leq TMax_w \\ u - 2^w & \text{se } u > TMax_w \end{cases}$$

> [!example] Exemplos (w = 32)
> $U2T_{32}(4294967295) = 4294967295 - 2^{32} = -1$  
> $U2T_{32}(2147483648) = 2147483648 - 2^{32} = -2147483648 = TMin_{32}$

### Comportamento em C — Promoção implícita

> [!warning] Comparações mistas signed/unsigned em C
> Quando uma expressão mistura signed e unsigned, C **converte implicitamente o signed para unsigned** antes da operação. Resultado frequentemente surpreendente:
>
> | Expressão | Tipo efetivo | Resultado |
> |---|---|---|
> | `-1 < 0U` | unsigned | **falso** (`-1` vira `UMax`) |
> | `-2147483647-1 < 2147483648U` | unsigned | **falso** (`TMin` = `TMin+2^32` unsigned) |
> | `(unsigned)-1 > -2` | unsigned | **verdadeiro** |
>
> **Regra:** se um operando é `unsigned`, o outro é convertido para `unsigned`.

---

## Extensão de Números

Ao ampliar uma representação de w para w' bits (w' > w):

### Zero extension — Unsigned

Adiciona zeros à esquerda. Preserva o valor exato:

```
[uw-1, uw-2, ..., u0]  →  [0, ..., 0, uw-1, ..., u0]
```

### Extensão de sinal — Signed (complemento de dois)

Copia o bit de sinal $x_{w-1}$ para as novas posições à esquerda:

```
[xw-1, xw-2, ..., x0]  →  [xw-1, ..., xw-1, xw-1, xw-2, ..., x0]
```

Preserva o valor: o par de bits `(-2^w + 2^(w-1)) = -2^(w-1)` — mesmo peso que o bit de sinal original.

> [!example] Exemplo: short → int (16 → 32 bits)
> `short sx = -12345` → bits `0xCFC7`  
> `int x = sx` → bits `0xFFFFCFC7` (16 zeros estendidos como 1s por ser negativo)  
> `unsigned ux = (unsigned short) sx` → bits `0x0000CFC7` (zero extension)

> [!note] Ordem das conversões em C
> `(unsigned) sx` em C converte **primeiro** o tamanho (short → int, com sign extension), **depois** o tipo (int → unsigned). Equivalente a `(unsigned)(int) sx`, não `(unsigned)(unsigned short) sx`.

---

## Truncamento de Números

Ao reduzir de w para k bits (k < w), os bits de alta ordem são descartados:

### Unsigned

$$B2U_k([x_{k-1}, \ldots, x_0]) = B2U_w([x_{w-1}, \ldots, x_0]) \bmod 2^k$$

O valor resultante é o residual módulo $2^k$.

### Signed (complemento de dois)

1. Computar $x \bmod 2^k$ (tratando x como unsigned)
2. Interpretar o resultado como complemento de dois de k bits: $U2T_k(x \bmod 2^k)$

> [!example] Exemplo: int → short (32 → 16 bits)
> `x = 53191` (0x0000CFC7) → truncado para 16 bits → `0xCFC7`  
> $53191 \bmod 65536 = 53191$. $U2T_{16}(53191) = 53191 - 65536 = -12345$

---

## Precisão Finita

Computadores realizam aritmética com **número fixo de bits** — aritmética de precisão finita. O conjunto de números representáveis não é fechado em relação às operações básicas:

| Operação | Problema | Tipo |
|---|---|---|
| 600 + 600 = 1200 (3 dígitos decimais) | Muito grande | Overflow |
| 3 − 5 = −2 | Negativo | Overflow |
| 50 × 50 = 2500 | Muito grande | Overflow |
| 7 / 2 = 3,5 | Não inteiro | Resultado fora do conjunto |

> [!warning] Leis algébricas não valem
> Na aritmética de precisão finita, leis como associatividade e distributividade podem não valer porque resultados intermediários podem exceder a representação.

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

### Valores especiais IEEE 754

Além de números normalizados, o padrão define quatro tipos adicionais:

| Tipo | Expoente | Mantissa | Bit implícito |
|---|---|---|---|
| **Normalizado** | 0 < exp < máx | qualquer | 1 |
| **Não normalizado** | 0 | ≠ 0 | 0 |
| **Zero (±0)** | 0 | 0 | 0 |
| **Infinito (±∞)** | todos 1s | 0 | — |
| **NaN** | todos 1s | ≠ 0 | — |

**Números não normalizados (denormalized):** evitam salto abrupto para zero quando o resultado é menor que o menor número normalizado. O expoente fica fixo em 0 e os bits da mantissa representam valores cada vez menores — perda gradual de significância em vez de *flush-to-zero*.

**Infinito:** gerado por overflow ou por divisão finita por zero. Comporta-se matematicamente: ∞ + n = ∞; n / ∞ = 0; qualquer finito / 0 = ∞.

**NaN (Not a Number):** resultado de operações indefinidas (∞/∞, 0/0, √−1). Propaga-se em cálculos subsequentes.

### Regiões da linha de números de ponto flutuante

```
excesso neg | negativos normais | falta neg | 0 | falta pos | positivos normais | excesso pos
            −maxfloat           −minfloat       minfloat    maxfloat
```

- Regiões de **excesso** (|x| > maxfloat): resultado = ±∞
- Regiões de **falta** (0 < |x| < minfloat normalizado): números não normalizados → zero com perda de precisão

O menor número não normalizado de precisão simples ≈ 2⁻¹⁴⁹ ≈ 1,4 × 10⁻⁴⁵.

### Modos de arredondamento IEEE 754

Ponto flutuante só aproxima valores reais. O IEEE 754 define quatro modos de arredondamento:

| Modo | Descrição | Bias |
|---|---|---|
| **Round-to-even** (padrão) | Arredonda para o representável mais próximo; em caso de empate, para o que tem LSB = 0 (par) | Nenhum |
| **Round-toward-zero** | Trunca em direção a zero | Subestima positivos, superestima negativos |
| **Round-down** | Arredonda em direção a −∞ | Sempre $x^- \leq x$ |
| **Round-up** | Arredonda em direção a +∞ | Sempre $x \leq x^+$ |

**Round-to-even** (também chamado *round-to-nearest*) é o padrão porque evita viés estatístico: em um grande conjunto de arredondamentos de meio-caminho, 50% vão para cima e 50% para baixo.

> [!example] Round-to-even em binário
> Arredondar para o bit 0 (LSB do resultado):
> - `10.111₂` (2⁷⁄₈) → empate: `11.0₂` (3) pois LSB=0 é par ✓
> - `10.101₂` (2⁵⁄₈) → arredonda para cima: `11.0₂` (3) — mais próximo
> - `11.010₂` (3¼) → arredonda para baixo: `11.0₂` (3) — mais próximo

### Propriedades da aritmética de ponto flutuante

| Propriedade | Inteiro | Ponto flutuante |
|---|---|---|
| Comutatividade | ✓ | ✓ |
| Associatividade | ✓ | **✗** |
| Distributividade | ✓ | **✗** |
| Monotonicidade (a ≥ b ⟹ a+c ≥ b+c) | ✓ | ✓ (exceto NaN) |
| $a \times a \geq 0$ | nem sempre | ✓ (exceto NaN) |

> [!warning] Não-associatividade em ponto flutuante
> `(3.14 + 1e20) - 1e20 = 0.0` — o valor 3.14 é perdido por arredondamento  
> `3.14 + (1e20 - 1e20) = 3.14` — resultado diferente com mesma associatividade

### Conversões de tipo em C (int / float / double)

| De → Para | Comportamento |
|---|---|
| `int` → `float` | Sem overflow; pode ser arredondado (23 bits de fração vs 32 bits int) |
| `int` → `double` | Exato — double tem 52 bits de fração, suficiente para qualquer int de 32 bits |
| `double` → `float` | Pode overflow (±∞); pode ser arredondado |
| `float`/`double` → `int` | Trunca para zero; overflow produz valor indefinido (tipicamente `TMin` em x86) |

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
- [[Conversão de Bases]] — algoritmos de conversão decimal↔binário, agrupamento octal/hex
- [[ULA]] — a ULA realiza operações tanto com inteiros quanto com ponto flutuante
- [[Aritmética Inteira]] — overflow, detecção, otimização por shift, vulnerabilidades de segurança
- [[Tipos de Dados C]] — int, unsigned, float, double; promoção de tipos e printf
