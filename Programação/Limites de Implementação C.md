---
title: Limites de Implementação C
aliases:
  - limits.h
  - float.h
  - INT_MAX C
  - DBL_MAX C
  - limites numéricos C
  - CHAR_MAX
tags:
  - computação/programação/c
date: 2026-04-21
---

# Limites de Implementação C

## Definição

`<limits.h>` define constantes para tamanhos de tipos integrais. `<float.h>` define constantes para tipos de ponto flutuante. Os valores listados são mínimos aceitáveis — implementações podem usar valores maiores.

Fonte: K&R Appendix B, §B.11.

## Relações (SPO)

| Sujeito | Predicado | Objeto |
|---|---|---|
| `<limits.h>` | define | limites de tipos inteiros |
| `<float.h>` | define | limites de tipos de ponto flutuante |
| valores listados | são | mínimos garantidos pelo padrão ANSI |
| implementação real | pode ter | limites maiores |

## `<limits.h>` — tipos inteiros

| Constante | Mínimo garantido | Significado |
|---|---|---|
| `CHAR_BIT` | 8 | bits em `char` |
| `CHAR_MAX` | `UCHAR_MAX` ou `SCHAR_MAX` | máximo de `char` |
| `CHAR_MIN` | 0 ou `SCHAR_MIN` | mínimo de `char` |
| `SCHAR_MAX` | +127 | máximo de `signed char` |
| `SCHAR_MIN` | -127 | mínimo de `signed char` |
| `UCHAR_MAX` | 255 | máximo de `unsigned char` |
| `SHRT_MAX` | +32767 | máximo de `short` |
| `SHRT_MIN` | -32767 | mínimo de `short` |
| `USHRT_MAX` | 65535 | máximo de `unsigned short` |
| `INT_MAX` | +32767 | máximo de `int` |
| `INT_MIN` | -32767 | mínimo de `int` |
| `UINT_MAX` | 65535 | máximo de `unsigned int` |
| `LONG_MAX` | +2147483647 | máximo de `long` |
| `LONG_MIN` | -2147483647 | mínimo de `long` |
| `ULONG_MAX` | 4294967295 | máximo de `unsigned long` |

> [!info] Em plataformas modernas de 64 bits
> `int` = 32 bits → `INT_MAX` = 2147483647. `long` = 64 bits → `LONG_MAX` = 9223372036854775807. Os valores da tabela são apenas mínimos do padrão ANSI C89.

## `<float.h>` — tipos de ponto flutuante

| Constante | Mínimo garantido | Significado |
|---|---|---|
| `FLT_RADIX` | 2 | base da representação de ponto flutuante |
| `FLT_DIG` | 6 | dígitos decimais de precisão (`float`) |
| `FLT_EPSILON` | 1E-5 | menor `x` tal que `1.0f + x ≠ 1.0f` |
| `FLT_MAX` | 1E+37 | maior valor finito `float` |
| `FLT_MIN` | 1E-37 | menor valor `float` normalizado positivo |
| `DBL_DIG` | 10 | dígitos decimais de precisão (`double`) |
| `DBL_EPSILON` | 1E-9 | menor `x` tal que `1.0 + x ≠ 1.0` |
| `DBL_MAX` | 1E+37 | maior valor finito `double` |
| `DBL_MIN` | 1E-37 | menor valor `double` normalizado positivo |

## Uso típico

```c
#include <limits.h>
#include <float.h>

/* verificar overflow antes de operação */
if (a > INT_MAX - b) { /* overflow na adição */ }

/* comparar ponto flutuante com tolerância */
#define EPSILON DBL_EPSILON
if (fabs(a - b) < EPSILON) { /* a ≈ b */ }

/* tamanho de buffer seguro */
char buf[sizeof("-") + sizeof(long) * CHAR_BIT / 3 + 2];
/* espaço para representação decimal de long */
```

> [!warning] `INT_MIN` não é `-INT_MAX`
> Em complemento de 2 (padrão atual), `INT_MIN = -2147483648` enquanto `INT_MAX = +2147483647`. Usar `-INT_MIN` causa overflow!

## Ver também

- [[Tipos de Dados C]] — tipos básicos e sua aritmética
- [[Biblioteca math.h]] — `ERANGE`, `HUGE_VAL` para erros de range
- [[Sistemas de Representação]] — complemento de 2, IEEE 754
- [[Aritmética Inteira]] — overflow e underflow
