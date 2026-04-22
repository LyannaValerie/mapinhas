---
title: Pré-processador C
aliases:
  - preprocessador C
  - "#define C"
  - "#include C"
  - macro C
  - compilação condicional C
  - CPP
tags:
  - computação/fundamentos
date: 2026-04-21
---

# Pré-processador C

## Definição

Etapa conceitual anterior à compilação. Processa diretivas `#` antes que o compilador veja o código. Principais recursos: inclusão de arquivos (`#include`), substituição de tokens (`#define`), compilação condicional (`#if`/`#ifdef`).

## Relações (SPO)
- `#include` → substitui linha por → conteúdo do arquivo
- `#define` → substitui token por → texto de substituição
- Macro com argumentos → expande em → código inline
- Compilação condicional → inclui/exclui → trechos do código em tempo de compilação

---

## 4.11.1 Inclusão de arquivo (`#include`)

```c
#include "arquivo.h"    /* busca no diretório do fonte, depois padrão */
#include <arquivo.h>    /* busca somente no caminho padrão */
```

- Linha substituída pelo conteúdo completo do arquivo
- Arquivos incluídos podem conter seus próprios `#include`
- Uso principal: centralizar `#define`s, protótipos, declarações `extern`

```c
#include <stdio.h>     /* declara printf, getchar, etc. */
#include "calc.h"      /* definições compartilhadas do projeto */
```

`#include` é o mecanismo preferido para conectar partes de programas grandes. Garante que todos os arquivos recebem as mesmas declarações.

---

## 4.11.2 Substituição por macro (`#define`)

### Constantes simbólicas

```c
#define MAXLINE 1000
#define YES     1
#define forever for (;;)   /* palavra nova */
```

- Nome tem mesma forma de variável
- Escopo: do ponto de definição até fim do arquivo
- Substituição **não** ocorre dentro de strings: `printf("YES")` permanece inalterado
- Substituição **não** ocorre como substring: `YESMAN` não é afetado por `#define YES`

### Macros com argumentos

```c
#define max(A, B)  ((A) > (B) ? (A) : (B))
```

`x = max(p+q, r+s)` expande para `x = ((p+q) > (r+s) ? (p+q) : (r+s))`.

Funciona para qualquer tipo — não precisa de versões separadas.

> [!warning] Armadilhas de macro
> **Dupla avaliação**: `max(i++, j++)` incrementa o maior **duas vezes**.
>
> **Parênteses**: `#define square(x) x*x` com `square(z+1)` expande para `z+1*z+1` — **errado**. Correto: `((x)*(x))`.
>
> Regra: sempre parentetizar os argumentos e o resultado completo.

### `#undef`

Remove definição anterior:

```c
#undef getchar
int getchar(void) { ... }   /* garante que é função, não macro */
```

### Stringificação (`#`)

`#` antes de parâmetro converte para string literal:

```c
#define dprint(expr) printf(#expr " = %g\n", expr)
dprint(x/y)
/* expande para: printf("x/y" " = %g\n", x/y); */
/* resultado:    printf("x/y = %g\n", x/y);     */
```

### Concatenação de tokens (`##`)

```c
#define paste(front, back) front ## back
paste(name, 1)   /* → name1 */
```

---

## 4.11.3 Compilação condicional

### `#if` / `#elif` / `#else` / `#endif`

```c
#if SYSTEM == SYSV
    #define HDR "sysv.h"
#elif SYSTEM == BSD
    #define HDR "bsd.h"
#elif SYSTEM == MSDOS
    #define HDR "msdos.h"
#else
    #define HDR "default.h"
#endif
#include HDR
```

`#if` avalia expressão inteira constante. `defined(nome)` vale 1 se nome foi definido.

### Guarda de inclusão múltipla

Padrão para evitar que `hdr.h` seja incluído mais de uma vez:

```c
/* hdr.h */
#if !defined(HDR)
#define HDR
/* conteúdo do arquivo */
#endif
```

Ou equivalente com `#ifndef`:

```c
#ifndef HDR
#define HDR
/* conteúdo do arquivo */
#endif
```

Primeira inclusão define `HDR`; inclusões subsequentes pulam para `#endif`.

---

## Ver também
- [[Escopo em C]] — arquivos de cabeçalho, `extern`, múltiplos arquivos
- [[Variáveis Estáticas em C]] — visibilidade entre arquivos
- [[Funções em C]] — protótipos em headers
- [[Linguagem C]] — estrutura geral de programa C
