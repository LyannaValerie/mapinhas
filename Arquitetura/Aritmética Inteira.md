---
title: Aritmética Inteira
aliases:
  - integer overflow
  - overflow de inteiros
  - adição sem sinal
  - adição em complemento de dois
  - overflow positivo
  - overflow negativo
  - negação sem sinal
  - multiplicação por potência de 2
  - divisão por potência de 2
  - deslocamento aritmético
  - deslocamento lógico
  - shift left
  - shift right
  - saturating add
tags:
  - computação/arquitetura
  - computação/fundamentos
date: 2026-04-13
source: "Computer Systems: A Programmer's Perspective — Bryant & O'Hallaron, Cap. 2"
---

# Aritmética Inteira

Computadores realizam aritmética de **precisão finita** — word size w fixo. Resultados que excedem a representação causam **overflow**. O comportamento é determinado pela aritmética modular (mod 2^w), não pela aritmética inteira convencional.

→ Representações subjacentes: [[Sistemas de Representação]]  
→ Implementação física: [[ULA]]  
→ Vulnerabilidades: [[Proteção de Memória x86]]

---

## Relações (SPO)

- Overflow sem sinal → produz → resultado mod 2^w (descarta bits excedentes)
- Overflow em complemento de dois → produz → wrap-around (positivo ou negativo)
- Deslocamento à esquerda (`<<`) → equivale a → multiplicação por potência de 2
- Deslocamento lógico à direita (`>>`) → equivale a → divisão por 2^k para unsigned
- Deslocamento aritmético à direita (`>>`) → equivale a → divisão por 2^k para signed, arredondando para baixo
- Compiladores → substituem → multiplicações por constante com combinações de shift e add

---

## Adição Sem Sinal (Unsigned)

Para x, y no intervalo $[0, 2^w - 1]$:

$$x +_w^u y = \begin{cases} x + y & \text{se } x + y < 2^w \quad \text{(normal)} \\ x + y - 2^w & \text{se } 2^w \leq x + y < 2^{w+1} \quad \text{(overflow)} \end{cases}$$

Equivalente: $(x + y) \bmod 2^w$ — os bits excedentes são descartados.

### Detecção de overflow

Dado $s = x +_w^u y$: overflow ocorreu se e somente se $s < x$ (ou equivalentemente $s < y$).

> [!example] Exemplo (w = 4)
> $9 + 12 = 21$. Em 4 bits: $21 \bmod 16 = 5$.  
> Detecção: $5 < 9$ → overflow confirmado.

### Negação sem sinal

$$-_w^u x = \begin{cases} 0 & \text{se } x = 0 \\ 2^w - x & \text{se } x > 0 \end{cases}$$

---

## Adição em Complemento de Dois

Para x, y no intervalo $[-2^{w-1},\ 2^{w-1} - 1]$:

$$x +_w^t y = \begin{cases} x + y - 2^w & \text{se } x + y \geq 2^{w-1} & \text{(overflow positivo)} \\ x + y & \text{se } -2^{w-1} \leq x + y < 2^{w-1} & \text{(normal)} \\ x + y + 2^w & \text{se } x + y < -2^{w-1} & \text{(overflow negativo)} \end{cases}$$

**Propriedade fundamental:** a representação em bits de $x +_w^t y$ é **idêntica** à de $x +_w^u y$. A mesma instrução de máquina serve para ambos.

### Detecção de overflow

| Tipo | Condição |
|---|---|
| Overflow positivo | $x > 0$ e $y > 0$ mas $s \leq 0$ |
| Overflow negativo | $x < 0$ e $y < 0$ mas $s \geq 0$ |

> [!warning] Armadilha comum em C
> A expressão `(sum - x == y) && (sum - y == x)` **não detecta overflow**.
> A aritmética de complemento de dois forma um grupo abeliano: `(x + y) - x == y` é sempre verdadeiro, com ou sem overflow.

### Negação em complemento de dois

$$-_w^t x = \begin{cases} TMin_w & \text{se } x = TMin_w \\ -x & \text{se } x > TMin_w \end{cases}$$

$TMin$ é seu próprio inverso aditivo — não existe valor positivo correspondente.

**No nível de bits:** `~x + 1 == -x` para qualquer inteiro (inversão de todos os bits + 1).

---

## Multiplicação

### Sem sinal

$$x *_w^u y = (x \cdot y) \bmod 2^w$$

### Complemento de dois

$$x *_w^t y = U2T_w\!\left((x \cdot y) \bmod 2^w\right)$$

> [!note] Equivalência bit a bit
> A representação em bits de $x *_w^u y$ e $x *_w^t y$ é **idêntica** para os w bits inferiores do produto. A mesma instrução de máquina serve para ambos — o que diferencia é a interpretação do resultado.

---

## Multiplicação por Potências de 2

Deslocamento à esquerda por k posições:

```
x << k  ==  x * 2^k
```

Válido para unsigned e complemento de dois, mesmo em caso de overflow (o wrap-around é idêntico).

### Otimização por compiladores

Multiplicações por constante $K$ são substituídas por combinações de shift, adição e subtração — operações muito mais rápidas que a instrução de multiplicação.

Para $K$ com bits 1 consecutivos do bit $n$ ao bit $m$ ($n \geq m$):

| Forma | Expressão | Operações |
|---|---|---|
| **A** | `(x<<n) + (x<<(n-1)) + ... + (x<<m)` | $n - m + 1$ shifts, $n - m$ adds |
| **B** | `(x<<(n+1)) - (x<<m)` | 2 shifts, 1 sub |

> [!example] Exemplo: `x * 14`
> $14 = 2^3 + 2^2 + 2^1$ (bits 3 a 1)  
> Forma A: `(x<<3) + (x<<2) + (x<<1)` — 3 shifts, 2 adds  
> $14 = 2^4 - 2^1$  
> Forma B: `(x<<4) - (x<<1)` — 2 shifts, 1 sub (**preferível**)

---

## Divisão por Potências de 2

### Unsigned — deslocamento lógico à direita

```c
x >> k  ==  x / 2^k    /* arredonda para baixo (em direção a zero) */
```

Zeros preenchidos à esquerda. Garantido ser divisão com truncamento para unsigned.

### Signed — deslocamento aritmético à direita

```c
x >> k  ==  ⌊x / 2^k⌋  /* arredonda para baixo (em direção a -∞) */
```

Cópias do bit de sinal preenchidas à esquerda (deslocamento aritmético). Para negativos, o resultado é **menor** (mais negativo) do que a divisão inteira convencional.

> [!warning] Arredondamento diverge para negativos
> Divisão inteira em C trunca para zero: $-13 / 2 = -6$  
> Shift aritmético arredonda para baixo: $-13\ >>\ 1 = -7$

### Correção para arredondamento para zero em negativos

Para obter resultado equivalente à divisão inteira de C (`x/2^k`):

```c
(x + (1<<k) - 1) >> k      /* adiciona bias=(2^k - 1) antes do shift */
```

Ou, de forma geral:

```c
(x < 0 ? x + (1<<k) - 1 : x) >> k
```

O bias `(2^k - 1)` só tem efeito quando x não é divisível por $2^k$.

> [!example] Exemplo
> $x = -13$, $k = 1$ → bias = 1 → $(-13 + 1) >> 1 = -12 >> 1 = -6$ ✓  
> $x = -12$, $k = 1$ → bias = 1 → $(-12 + 1) >> 1 = -11 >> 1 = -6$ ✓ (sem bias: $-12 >> 1 = -6$ também funciona quando divisível)

---

## Vulnerabilidades de Segurança

Overflow de inteiros é causa frequente de vulnerabilidades críticas.

> [!danger] Overflow de multiplicação → buffer overflow
> ```c
> void *result = malloc(ele_cnt * ele_size);   /* pode overflow para valor pequeno */
> memcpy(result, src, ele_cnt * ele_size);     /* escreve além do buffer */
> ```
> Se `ele_cnt = 2^20 + 1` e `ele_size = 4096 = 2^12`, o produto em 32 bits overflow para 4096 bytes — muito menos que os ~4 GB reais necessários. O `memcpy` subsequente sobrescreve memória além do buffer alocado.
>
> **Correção:** verificar overflow antes de alocar; usar `uint64_t` para o produto; declarar `maxlen` como `size_t` (unsigned).

> [!danger] Comparação signed com parâmetro negativo
> ```c
> int copy_from_kernel(void *dest, int maxlen) {
>     int len = KSIZE < maxlen ? KSIZE : maxlen;
>     memcpy(dest, kbuf, len);   /* se maxlen < 0 → len < 0 → size_t enorme */
> }
> ```
> `memcpy` declara `n` como `size_t` (unsigned). Passar `len = -1` equivale a passar $2^{32} - 1$ — lê praticamente toda a memória do kernel.

---

## Propriedades Algébricas

| Propriedade | Unsigned | Complemento de dois |
|---|---|---|
| Comutatividade | ✓ | ✓ |
| Associatividade | ✓ | ✓ |
| Distributividade (multiplicação) | ✓ | ✓ |
| Elemento neutro (adição = 0) | ✓ | ✓ |
| Inverso aditivo | ✓ | ✓ (exceto TMin) |
| Monotonicidade (a ≥ b ⟹ a+c ≥ b+c) | ✓ | ✓ |

> [!note] Contraste com ponto flutuante
> Ponto flutuante não é associativo: `(3.14 + 1e20) - 1e20 = 0.0` mas `3.14 + (1e20 - 1e20) = 3.14`. Ver [[Sistemas de Representação#Ponto Flutuante (IEEE 754)]].

---

## Ver também

- [[Sistemas de Representação]] — unsigned, complemento de dois, conversões T2U/U2T, extensão e truncamento
- [[ULA]] — hardware executor das operações aritméticas
- [[Tipos de Dados C]] — int, unsigned int, promoção implícita signed→unsigned em C
- [[Álgebra Booleana]] — operações bit a bit (~, &, |, ^) e aplicações em C
- [[Registradores x86]] — flags OF (overflow), CF (carry), SF (sign), ZF (zero)
- [[Linguagem Assembly]] — instrução `imul`/`mul`, `add`/`sub`, `sal`/`sar`/`shr`
