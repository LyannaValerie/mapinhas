---
title: Biblioteca math.h
aliases:
  - math.h
  - funções matemáticas C
  - sqrt C
  - sin cos tan C
tags:
  - computação/programação/c
date: 2026-04-21
---

# Biblioteca math.h

## Definição

`<math.h>` declara funções matemáticas. Todos os argumentos e retornos são `double` exceto onde indicado. Ângulos em radianos.

Fonte: K&R Appendix B, §B.4.

## Relações (SPO)

| Sujeito | Predicado | Objeto |
|---|---|---|
| `EDOM` | sinaliza | erro de domínio (argumento fora do domínio) |
| `ERANGE` | sinaliza | erro de intervalo (resultado fora de `double`) |
| `HUGE_VAL` | representa | valor `double` positivo para overflow |
| erro de domínio | define `errno` como | `EDOM` |
| erro de intervalo | define `errno` como | `ERANGE` |

## Funções trigonométricas

```c
sin(x)           /* seno de x */
cos(x)           /* cosseno de x */
tan(x)           /* tangente de x */
asin(x)          /* arcoseno, resultado em [-π/2, π/2], x ∈ [-1,1] */
acos(x)          /* arcocosseno, resultado em [0, π], x ∈ [-1,1] */
atan(x)          /* arcotangente, resultado em [-π/2, π/2] */
atan2(y, x)      /* arcotangente de y/x, resultado em [-π, π] */
```

## Funções hiperbólicas

```c
sinh(x)   cosh(x)   tanh(x)
```

## Funções exponenciais e logarítmicas

```c
exp(x)       /* e^x */
log(x)       /* ln(x), x > 0 */
log10(x)     /* log₁₀(x), x > 0 */
pow(x, y)    /* x^y; erro de domínio se x=0 e y≤0, ou x<0 e y não-inteiro */
sqrt(x)      /* √x, x ≥ 0 */
```

## Funções de arredondamento e valor absoluto

```c
ceil(x)      /* menor inteiro ≥ x, como double */
floor(x)     /* maior inteiro ≤ x, como double */
fabs(x)      /* |x| */
```

## Funções de decomposição

```c
double ldexp(x, n)          /* x * 2^n */
double frexp(x, int *exp)   /* decompõe x em mantissa m ∈ [0.5, 1) e expoente: x = m * 2^exp */
double modf(x, double *ip)  /* decompõe x em partes inteira (*ip) e fracionária (retorno) */
double fmod(x, y)           /* resto de ponto flutuante: x - n*y onde n = trunc(x/y) */
```

## Tratamento de erros

```c
#include <math.h>
#include <errno.h>

errno = 0;
double r = sqrt(-1.0);
if (errno == EDOM) { /* erro de domínio */ }

double big = pow(DBL_MAX, 2.0);
if (errno == ERANGE) { /* overflow */ }
```

> [!info] Compilação com `-lm`
> Em sistemas POSIX/Linux, linkar com `-lm` para incluir a biblioteca matemática:
> ```
> gcc prog.c -lm
> ```

## Ver também

- [[Tipos de Dados C]] — tipo `double`, ponto flutuante
- [[Biblioteca stdlib.h em C]] — `rand()`, `srand()` para números pseudo-aleatórios
- [[Limites de Implementação C]] — `DBL_MAX`, `FLT_EPSILON`
