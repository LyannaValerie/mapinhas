---
title: Operadores C
aliases:
  - operadores aritméticos C
  - operadores relacionais C
  - conversão de tipos C
  - cast C
  - operador ternário C
  - operador de atribuição composta C
  - precedência de operadores C
tags:
  - computação/fundamentos
date: 2026-04-21
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Programação]]

# Operadores C

## Definição

C fornece operadores aritméticos, relacionais, lógicos, bit a bit, de atribuição composta e condicional. Esta nota cobre os operadores não detalhados em [[Tipos de Dados C]] (bitwise, incremento/decremento) e [[Linguagem C]] (lógicos, atribuição como expressão).

---

## Operadores aritméticos

| Operador | Operação | Notas |
|---|---|---|
| `+` | adição | — |
| `-` | subtração | — |
| `*` | multiplicação | — |
| `/` | divisão | Trunca para inteiros; arredondamento de negativos é implementação-dependente |
| `%` | módulo (resto) | Apenas integrais; sinal do resultado é implementação-dependente para negativos |

**Precedência:**

```
unário + -          (maior)
* / %
binário + -         (menor)
```

Todos associam da esquerda para a direita.

```c
/* ano bissexto */
if ((year % 4 == 0 && year % 100 != 0) || year % 400 == 0)
    printf("bissexto\n");
```

> [!warning] `%` não se aplica a `float` ou `double`.

---

## Operadores relacionais e de igualdade

```
>   >=   <   <=      (mesma precedência)
==  !=               (menor que relacionais)
```

Relacionais têm precedência menor que aritméticos:

```c
i < lim - 1    /* equivale a: i < (lim-1) */
```

Valor de uma expressão relacional ou lógica: **1** se verdadeiro, **0** se falso.

O operador `!` (NOT unário) converte operando não-zero em 0 e zero em 1:

```c
if (!valid)        /* equivale a: if (valid == 0) */
```

Ver também: [[Linguagem C#Operadores lógicos `&&` e `||`]] para short-circuit evaluation.

---

## Conversão de tipos

### Conversões implícitas (promoção)

Regra geral: o tipo "mais estreito" é promovido ao "mais largo" sem perda de informação.

**Hierarquia de promoção (sem operandos unsigned):**

```
long double  ←  double  ←  float  ←  int  ←  short/char
```

Regras em ordem de prioridade:
1. Se um operando é `long double` → converte o outro para `long double`
2. Senão, se um é `double` → converte para `double`
3. Senão, se um é `float` → converte para `float`
4. Senão, converte `char` e `short` para `int`
5. Então, se um é `long` → converte para `long`

> [!info] `float` não vira `double` automaticamente
> Diferente do K&R original. Funções matemáticas em `<math.h>` usam `double`. Use `float` para economizar memória em arrays grandes.

### Conversões em atribuição

O valor do lado direito é convertido para o tipo do lado esquerdo:

```c
int i;
char c;
i = c;    /* char → int (zero-extend ou sign-extend, dependente da máquina) */
c = i;    /* int → char: descarta bits altos */
```

**Truncamento**: inteiros mais longos para mais curtos descartam os bits de alta ordem.

### Conversões em chamadas de função

Sem protótipo: `char` e `short` → `int`; `float` → `double`.
Com protótipo: conversão automática conforme a declaração.

### Cast (conversão explícita)

```c
(tipo) expressão
```

Força a conversão; o original não é alterado:

```c
sqrt((double) n)    /* converte n para double antes de passar para sqrt */
```

O cast tem a mesma precedência dos outros operadores unários (alta).

> [!tip] Com protótipo, cast é desnecessário
> ```c
> double sqrt(double);
> root2 = sqrt(2);    /* 2 é coercionado automaticamente para 2.0 */
> ```

---

## Operadores de atribuição composta

Forma geral: `expr1 op= expr2` equivale a `expr1 = (expr1) op (expr2)` com `expr1` calculada apenas uma vez.

| Operador | Exemplo | Equivalente |
|---|---|---|
| `+=` | `i += 2` | `i = i + 2` |
| `-=` | `x -= 1` | `x = x - 1` |
| `*=` | `x *= y+1` | `x = x * (y+1)` |
| `/=` | `n /= 2` | `n = n / 2` |
| `%=` | `n %= 10` | `n = n % 10` |
| `<<=` | `x <<= 1` | `x = x << 1` |
| `>>=` | `x >>= 1` | `x = x >> 1` |
| `&=` | `x &= mask` | `x = x & mask` |
| `^=` | `x ^= mask` | `x = x ^ mask` |
| `\|=` | `x \|= mask` | `x = x \| mask` |

> [!note] Nota: parênteses importam
> `x *= y + 1` ≡ `x = x * (y+1)`, **não** `x = x*y + 1`.

**Exemplo — contar bits 1:**

```c
int bitcount(unsigned x)
{
    int b;
    for (b = 0; x != 0; x >>= 1)
        if (x & 01)
            b++;
    return b;
}
```

---

## Expressão condicional (ternário `?:`)

```c
expr1 ? expr2 : expr3
```

Se `expr1` é verdadeiro (não-zero), o valor é `expr2`; senão, `expr3`. Apenas uma das duas é avaliada.

```c
z = (a > b) ? a : b;    /* z = máximo de a e b */
```

Tipo do resultado: determinado pelas regras de conversão se `expr2` e `expr3` tiverem tipos diferentes.

**Precedência**: muito baixa — logo acima de atribuição. Parênteses em torno da condição são recomendados por clareza (embora não obrigatórios).

**Usos idiomáticos:**

```c
/* imprimir array 10 por linha */
printf("%6d%c", a[i], (i%10==9 || i==n-1) ? '\n' : ' ');

/* plural condicional */
printf("You have %d item%s.\n", n, n==1 ? "" : "s");
```

---

## Tabela de precedência e associatividade

| Operadores | Associatividade |
|---|---|
| `()` `[]` `->` `.` | Esquerda → direita |
| `!` `~` `++` `--` `+` `-` `*` `(tipo)` `sizeof` | Direita → esquerda (unários) |
| `*` `/` `%` | Esquerda → direita |
| `+` `-` | Esquerda → direita |
| `<<` `>>` | Esquerda → direita |
| `<` `<=` `>` `>=` | Esquerda → direita |
| `==` `!=` | Esquerda → direita |
| `&` | Esquerda → direita |
| `^` | Esquerda → direita |
| `\|` | Esquerda → direita |
| `&&` | Esquerda → direita |
| `\|\|` | Esquerda → direita |
| `?:` | Direita → esquerda |
| `=` `+=` `-=` `*=` `/=` `%=` `&=` `^=` `\|=` `<<=` `>>=` | Direita → esquerda |
| `,` | Esquerda → direita |

> [!warning] `&`, `^`, `|` têm precedência **menor** que `==` e `!=`
> Sempre usar parênteses em expressões bit a bit combinadas com comparações:
> ```c
> if ((x & MASK) == 0) ...    /* correto */
> if (x & MASK == 0) ...      /* errado: == avaliado primeiro */
> ```

---

## Ordem de avaliação

C **não especifica** a ordem de avaliação dos operandos (exceto `&&`, `||`, `?:`, `,`).

```c
x = f() + g();    /* f pode ser avaliado antes ou depois de g */

printf("%d %d\n", ++n, power(2, n));    /* ERRADO: n pode ser incrementado antes/depois */

/* correto: */
++n;
printf("%d %d\n", n, power(2, n));
```

> [!danger] Side effects em expressões
> `a[i] = i++` — subscrito é o valor antigo ou novo de `i`? Comportamento **indefinido**. Evitar.
> Não escrever código que depende da ordem de avaliação de subexpressões.

---

## Enumerações

Alternativa a `#define` para constantes nomeadas:

```c
enum boolean { NO, YES };                     /* NO=0, YES=1 */
enum escapes { BELL='\a', TAB='\t', NL='\n' };
enum months { JAN=1, FEB, MAR, ..., DEC };    /* FEB=2, MAR=3, etc. */
```

- Valores não especificados continuam a sequência do último especificado
- Nomes em enumerações diferentes devem ser distintos
- Vantagem sobre `#define`: depurador pode imprimir valores na forma simbólica

---

## Ver também
- [[Tipos de Dados C]] — bitwise, incremento/decremento, tipos integrais
- [[Linguagem C]] — operadores lógicos `&&`/`||`; atribuição como expressão
- [[Funções em C]] — protótipos; passagem de argumentos e conversão de tipos
- [[Arrays em C]] — subscrito como expressão inteira
