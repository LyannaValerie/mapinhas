---
title: Sistemas de Representação
aliases:
  - Dados Binários
  - números binários
  - representação numérica
  - complemento de dois
  - complemento de um
  - magnitude com sinal
  - sistema binário
  - sistema octal
  - sistema hexadecimal
  - conversão de base
  - frações binárias
  - IEEE 754
  - ponto flutuante IEEE
  - precisão simples
  - precisão dupla
  - significando
  - expoente tendenciado
  - excesso 127
  - excesso 1023
tags:
  - computação/arquitetura
  - computação/fundamentos
date: 2026-04-21
source: "Organização Estruturada de Computadores — Tanenbaum, Apêndices A e B"
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Sistemas de Representação

Computadores operam com **números de precisão finita** — a memória para armazenar um número é fixada em projeto. Resultados fora dessa faixa produzem erros de excesso (overflow) ou falta (underflow).

→ Aritmética sobre inteiros: [[Aritmética Inteira]]
→ Hardware executor: [[ULA]]
→ Unidade de ponto flutuante: [[FPU]]

---

## Relações (SPO)

- Número de precisão finita → não é fechado em relação a → adição, subtração, multiplicação, divisão
- Sistema binário → usa → base 2 (dígitos 0 e 1)
- Bit → é → um dígito binário (0 ou 1)
- Complemento de dois → é → representação padrão de inteiros com sinal em computadores
- IEEE 754 → padroniza → aritmética de ponto flutuante em base 2
- Significando → compõe → número IEEE 754 junto com expoente e bit de sinal

---

## Apêndice A — Números Binários

### A.1 Números de Precisão Finita

Conjunto de inteiros positivos de 3 dígitos decimais: 1.000 membros (000–999). Impossível expressar:

1. Números > 999
2. Negativos
3. Frações
4. Irracionais
5. Complexos

Aritmética sobre esse conjunto não é fechada:

| Operação | Resultado | Problema |
|---|---|---|
| 600 + 600 | 1.200 | muito grande (overflow) |
| 003 − 005 | −2 | negativo (underflow) |
| 050 × 050 | 2.500 | muito grande (overflow) |
| 007 / 002 | 3,5 | não é inteiro |

### A.2 Sistemas Numéricos Posicionais

Forma geral de número decimal: $d_n \cdot 10^n + d_{n-1} \cdot 10^{n-1} + \ldots + d_0 \cdot 10^0$

| Sistema | Base | Dígitos |
|---|---|---|
| Binário | 2 | 0, 1 |
| Octal | 8 | 0–7 |
| Decimal | 10 | 0–9 |
| Hexadecimal | 16 | 0–9, A–F |

> [!example] O número 2.001 nas quatro bases
> - Binário: 11111010001
> - Octal: 3721
> - Decimal: 2001
> - Hexadecimal: 7D1

### A.3 Conversão de Bases

**Binário → Octal/Hex:** agrupar bits (3 por dígito octal, 4 por dígito hex). Adicionar zeros à esquerda se necessário.

**Decimal → Binário:** divisões sucessivas por 2; os restos em ordem inversa formam o resultado.

**Binário → Decimal:** somar $b_i \cdot 2^i$ para cada bit $b_i$.

> [!example] Decimal 1.492 → Binário
> 1492 ÷ 2 = 746 r **0**
> 746 ÷ 2 = 373 r **0**
> 373 ÷ 2 = 186 r **1**
> ... → lendo os restos de baixo para cima

### A.4 Números Negativos

Três sistemas históricos:

| Sistema | Bit de sinal | Problema |
|---|---|---|
| **Magnitude com sinal** | 0=+, 1=− | dois zeros (+0 e −0) |
| **Complemento de um** | 0=+, 1=− | dois zeros; obsoleto |
| **Complemento de dois** | 0=+, 1=− | padrão atual; um único zero |

**Complemento de dois:** inverter todos os bits e somar 1.

> [!example] −6 em 8 bits
> +6 = 00000110
> Complemento de um: 11111001
> Complemento de dois: 11111010 (= −6)

**Faixa com w bits:** $[-2^{w-1},\ 2^{w-1}-1]$

**Singularidade:** o padrão $100\ldots0$ é seu próprio complemento — $TMin$ não tem correspondente positivo.

**Detecção de overflow:** ocorre se e somente se o vai-um para o bit de sinal difere do vai-um do bit de sinal.

Também existe o sistema de **excesso-$2^{n-1}$**: o número $x$ é armazenado como $x + 2^{n-1}$. Isomórfico ao complemento de dois com o bit de sinal invertido.

### A.5 Aritmética Binária

Tabela de adição: 0+0=0, 0+1=1, 1+0=1, 1+1=10 (escreve 0, vai-um 1).

Em complemento de dois, o vai-um gerado pela posição mais à esquerda é simplesmente descartado.

> [!note] Frações binárias
> Ponto binário funciona como ponto decimal: $1.101_2 = 1 + 0.5 + 0 + 0.125 = 1.625_{10}$.
> Conversão decimal → binário fracionário: multiplicações sucessivas por 2; parte inteira de cada resultado forma o dígito.

---

## Apêndice B — Ponto Flutuante IEEE 754

### B.1 Princípios de Ponto Flutuante

Forma geral: $\pm f \times R^e$, onde $f$ é a mantissa (fração) e $e$ o expoente inteiro.

**Normalização:** manter $0.1 \leq |f| < 1$ (ou $1 \leq |f| < 2$ no IEEE). Garante representação única.

Diferenças entre ponto flutuante e números reais:
- **Buracos**: entre dois números FP consecutivos há uma infinidade de reais não representáveis
- **Densidade não uniforme**: números pequenos estão mais próximos entre si
- **Não associatividade**: $(a + b) + c \neq a + (b + c)$ em geral

### B.2 Padrão IEEE 754 (1985)

Criado pelo IEEE no final dos anos 1970, liderado por William Kahan (Berkeley). Antes, cada fabricante tinha seu próprio formato — alguns com bugs.

**Formatos definidos:**

| Formato | Bits total | Sinal | Expoente | Mantissa |
|---|---|---|---|---|
| Precisão simples | 32 | 1 | 8 | 23 |
| Precisão dupla | 64 | 1 | 11 | 52 |
| Precisão estendida | 80 | 1 | 15 | 64 (uso interno FPU) |

**Layout (precisão simples):**
```
[S(1)] [Expoente(8)] [Mantissa(23)]
 bit31    bits30-23      bits22-0
```

**Expoente tendenciado (biased):**
- Simples: excesso 127 (bias = 127) — faixa −126 a +127
- Dupla: excesso 1023 (bias = 1023) — faixa −1022 a +1023
- Expoentes 0 e máximo (255/2047) reservados para casos especiais

**Significando (significand):** bit 1 implícito à esquerda do ponto binário + 23/52 bits explícitos. Todos os números normalizados têm significando $s$ tal que $1 \leq s < 2$.

> [!example] Exemplos em precisão simples (hex)
> | Valor | Hexadecimal |
> |---|---|
> | 0,5 | 3F000000 |
> | 1,0 | 3F800000 |
> | 1,5 | 3FC00000 |

### Características numéricas

| Item | Simples | Dupla |
|---|---|---|
| Sistema de expoente | Excesso 127 | Excesso 1023 |
| Faixa de expoente | −126 a +127 | −1022 a +1023 |
| Menor normalizado | ≈ 2^−126 | ≈ 2^−1022 |
| Maior normalizado | ≈ 2^128 | ≈ 2^1024 |
| Faixa decimal | ≈ 10^−38 a 10^38 | ≈ 10^−308 a 10^308 |
| Menor não normalizado | ≈ 10^−45 | ≈ 10^−324 |

### Tipos numéricos especiais

| Tipo | Exp | Mantissa | Notas |
|---|---|---|---|
| **Normalizado** | 0 < Exp < Máx | qualquer | bit 1 implícito |
| **Não normalizado** | 0 | ≠ 0 | bit 0 implícito; falta controlada |
| **Zero** | 0 | 0 | ±0 (dois zeros) |
| **Infinito** | todos 1s | 0 | ±∞; comportamento matemático correto |
| **NaN** | todos 1s | ≠ 0 | Not a Number; resultado indefinido |

**Números não normalizados (denormalizados):** quando resultado cai abaixo do menor normalizado, o expoente fica fixo em 0 e a mantissa perde bits significativos progressivamente ("falta gradual"). Menor não normalizado de precisão simples: $2^{-149}$ (expoente −126, mantissa = $2^{-23}$).

**Infinito:** overflow resulta em ±∞. Operações: ∞ + finito = ∞; finito / ∞ = 0; finito / 0 = ∞.

**NaN:** ∞/∞, 0/0, √(−1), etc. Pode ser operando com resultados previsíveis.

> [!warning] Ponto flutuante não é associativo
> `(3.14 + 1e20) - 1e20 = 0.0` mas `3.14 + (1e20 - 1e20) = 3.14`

---

## Ver também

- [[Aritmética Inteira]] — overflow, complemento de dois, aritmética modular (CS:APP)
- [[FPU]] — hardware que executa operações IEEE 754; registradores x87, SSE, AVX
- [[ULA]] — circuito executor das operações aritméticas inteiras
- [[Álgebra Booleana]] — operações bit a bit
- [[Flag]] — flags derivados de resultados aritméticos (carry, overflow, sign, zero)
- [[Processador 8088]] — contexto pedagógico: assembly com representações binárias
