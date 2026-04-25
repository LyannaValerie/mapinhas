---
title: Ponto Flutuante — Operações
aliases:
  - FP addition algorithm
  - FP multiplication algorithm
  - floating point addition
  - floating point multiplication
  - guard bit
  - round bit
  - sticky bit
  - ulp
  - MIPS FP instructions
  - coprocessador 1 MIPS
  - precisão simples operações
  - precisão dupla operações
tags:
  - computação/arquitetura
  - computação/fundamentos
  - computação/MIPS
date: 2026-04-23
source: "Patterson & Hennessy — Computer Organization and Design, Cap. 3"
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Ponto Flutuante — Operações

Operações aritméticas sobre números no formato [[Sistemas de Representação#Apêndice B — Ponto Flutuante IEEE 754|IEEE 754]]. Complementa a representação estática com os algoritmos e hardware necessários para add, sub, mul, div.

## Relações (SPO)

- Adição FP → requer → alinhamento de expoentes antes de somar significandos
- Normalização → é necessária → após toda operação FP
- Guard/round bits → aumentam → precisão do arredondamento intermediário
- Sticky bit → indica → se algum bit além do round foi perdido
- ulp → mede → erro em unidades do último bit significativo
- MIPS FP → usa → coprocessador 1 ($f0–$f31) separado da ULA inteira
- Dupla precisão MIPS → usa → pares de registradores ($f0/$f1, $f2/$f3, …)
- Ponto flutuante → não é associativo → $(a + b) + c \neq a + (b + c)$ em geral

---

## Algoritmo de Adição FP

### Passos (base decimal → aplica a binário)

```
Dados: X = s_X × 2^E_X,  Y = s_Y × 2^E_Y  (E_X ≥ E_Y)

Passo 1 — Alinhar expoentes:
  Deslocar s_Y à direita por (E_X − E_Y) bits
  Guardar bits deslocados em guard e round

Passo 2 — Somar significandos:
  s = s_X + s_Y'  (s_Y' = s_Y após deslocamento)

Passo 3 — Normalizar:
  Se |s| ≥ 2.0:  shift direita 1 bit, E++   (carry-out)
  Se |s| < 1.0:  shift esquerda k bits, E−=k  (cancelamento)
  Verificar overflow/underflow no expoente

Passo 4 — Arredondar:
  Ajustar para p bits de significando
  Se necessário, re-normalizar (passo 3 novamente)
```

### Exemplo

```
0.5ten − 0.4375ten em binário (4 bits de precisão):

  0.5     = 1.000 × 2⁻¹
−0.4375  = −1.110 × 2⁻²

Passo 1: −1.110 × 2⁻² → −0.111 × 2⁻¹  (shift direita 1)
Passo 2:  1.000 + (−0.111) = 0.001  × 2⁻¹
Passo 3:  0.001 × 2⁻¹ → 1.000 × 2⁻⁴  (normalizar: shift esquerda 3)
Resultado: +1.000 × 2⁻⁴ = 0.0625ten ✓
```

### Hardware

```
Expoente1, Expoente2
      │
   Small ALU ← diferença de expoentes → controla shift
      │
Shift menor significando à direita
      │
   Big ALU ← soma os significandos
      │
Normalização (shift esquerda/direita + ajuste de expoente)
      │
Arredondamento → resultado final (pode precisar re-normalizar)
```

---

## Algoritmo de Multiplicação FP

### Passos

```
Dados: X = (−1)^S_X × (1 + F_X) × 2^(E_X − bias)
       Y = (−1)^S_Y × (1 + F_Y) × 2^(E_Y − bias)

Passo 1 — Expoente:
  E_novo = E_X + E_Y − bias
  (com expoentes tendenciados: E_X_biased + E_Y_biased − bias)

Passo 2 — Significandos:
  s = (1 + F_X) × (1 + F_Y)   → produto de 2p bits

Passo 3 — Normalizar:
  Se s ≥ 2.0: shift direita 1 bit, E_novo++
  Verificar overflow/underflow

Passo 4 — Arredondar:
  Truncar para p bits; re-normalizar se necessário

Passo 5 — Sinal:
  S_resultado = S_X XOR S_Y
```

### Exemplo

```
1.110ten × 10¹⁰ × 9.200ten × 10⁻⁵  (4 dígitos decimais)

Passo 1: 10 + (−5) = 5 → expoente = 5
Passo 2: 1.110 × 9.200 = 10.212
Passo 3: 10.212 × 10⁵ → 1.0212 × 10⁶  (E = 6)
Passo 4: 1.022 × 10⁶  (4 dígitos)
Passo 5: positivo × positivo = positivo
```

---

## Arredondamento

### Bits extras

| Bit | Posição | Papel |
|---|---|---|
| **Guard** | 1º bit além da precisão | preserva o bit mais significativo descartado |
| **Round** | 2º bit além da precisão | ajuda na decisão de arredondamento |
| **Sticky** | OR de todos os demais bits descartados | indica se houve perda de informação além do round |

Sem guard e round: erro de até 1 ulp.
Com guard e round: erro ≤ 0.5 ulp (mais 1 bit extra de precisão efetiva).

### Round-to-Nearest-Even (padrão IEEE 754)

- Se guard = 0: truncar
- Se guard = 1 e (round OR sticky) = 1: arredondar para cima
- Se guard = 1 e round = sticky = 0 (exatamente no meio): arredondar para o número com LSB = 0 (par)

### ulp (Unit in the Last Place)

Medida de erro: diferença entre o resultado arredondado e o resultado exato em unidades do bit menos significativo do significando.

Resultado correto a 0.5 ulp → máxima precisão possível para p bits.

> [!warning] Ponto flutuante não é associativo
> `(3.14 + 1e20) − 1e20 = 0.0`  
> `3.14 + (1e20 − 1e20) = 3.14`  
> Compiladores não podem reordenar expressões FP livremente. Ver [[Aritmética Inteira#Propriedades Algébricas]].

---

## Números Especiais IEEE 754

| Expoente | Fração | Valor |
|---|---|---|
| 0 | 0 | ±0 |
| 0 | ≠ 0 | ±denormalizado ($0.frac × 2^{-126}$) |
| 1–254 | qualquer | ±normal ($1.frac × 2^{exp-127}$) |
| 255 | 0 | ±∞ |
| 255 | ≠ 0 | NaN |

**Denormalizados:** permitem underflow gradual — números menores que $2^{-126}$ com precisão reduzida.

**NaN:** resultado de 0/0, ∞−∞, etc. Permite adiar testes de validade.

---

## MIPS — Instruções de Ponto Flutuante

### Registradores

- 32 registradores FP: `$f0`–`$f31`
- Simples (single): qualquer `$f0`–`$f31`
- Dupla (double): pares `$f0`/`$f1`, `$f2`/`$f3`, … (MIPS-32 relaxa isso com `l.d`/`s.d`)
- Coprocessador 1 (CP1) — separado da ULA inteira

### Instruções Aritméticas

```mips
add.s  $f0, $f2, $f4    # $f0 = $f2 + $f4  (single)
sub.s  $f0, $f2, $f4    # $f0 = $f2 − $f4  (single)
mul.s  $f0, $f2, $f4    # $f0 = $f2 × $f4  (single)
div.s  $f0, $f2, $f4    # $f0 = $f2 / $f4  (single)

add.d  $f0, $f2, $f4    # double (usa pares de registradores)
sub.d  $f0, $f2, $f4
mul.d  $f0, $f2, $f4
div.d  $f0, $f2, $f4
```

### Transferência de Dados

```mips
lwc1  $f1, 100($s1)     # load word → coprocessador 1 (single)
swc1  $f1, 100($s1)     # store word ← coprocessador 1
l.d   $f4, 0($t2)       # load double (MIPS-32+)
s.d   $f4, 0($t2)       # store double
```

### Comparação e Desvio

```mips
c.lt.s  $f1, $f2        # flag FP = 1 se $f1 < $f2  (single)
c.le.s  $f1, $f2        # flag FP = 1 se $f1 ≤ $f2
c.eq.s  $f1, $f2        # flag FP = 1 se $f1 = $f2
bc1t    Label            # branch se flag FP = 1
bc1f    Label            # branch se flag FP = 0
```

(Sufixo `.d` para double precision nas comparações.)

### Conversão

```mips
cvt.s.w  $f0, $f1       # int → single
cvt.d.w  $f0, $f1       # int → double
cvt.s.d  $f0, $f1       # double → single
cvt.d.s  $f0, $f1       # single → double
```

### Limites de Expoente MIPS

| Precisão | Bias | Max exp | Min exp |
|---|---|---|---|
| Single | 127 | 127 | −126 |
| Double | 1023 | 1023 | −1022 |

---

## Exemplo: Multiplicação de Matrizes FP

```mips
# x[i][j] += y[i][k] * z[k][j]   (32×32 doubles)
mul.d $f16, $f18, $f16   # $f16 = y[i][k] * z[k][j]
add.d $f4,  $f4,  $f16   # $f4  = x[i][j] + produto
```

Endereçamento de elemento `[i][j]` em matriz de doubles (8 bytes cada):
```mips
sll  $t2, $s0, 5         # i * 32 (tamanho da linha)
addu $t2, $t2, $s1       # + j
sll  $t2, $t2, 3         # * 8 (byte offset, double = 8 bytes)
addu $t2, $a0, $t2       # endereço de x[i][j]
l.d  $f4, 0($t2)         # carrega double
```

---

## Ver também

- [[Sistemas de Representação]] — IEEE 754: formato, bias, denormalizados, NaN, ±∞
- [[Aritmética MIPS — Inteiros]] — mult/div inteiros, Hi/Lo, overflow exception
- [[Aritmética Inteira]] — propriedades algébricas, não-associatividade FP vs inteiro
- [[MIPS — Conjunto de Instruções]] — ISA completa com FP
- [[ULA]] — hardware de execução; unidade FP integrada desde 1990s
- [[Aritmética Decimal BCD]] — representação decimal e conversão via FPU x87
