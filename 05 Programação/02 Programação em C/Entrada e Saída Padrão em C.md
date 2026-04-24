---
title: Entrada e Saída Padrão em C
aliases:
  - stdin stdout C
  - getchar C
  - putchar C
  - I/O padrão C
  - entrada padrão C
  - saída padrão C
tags:
  - computação/fundamentos
date: 2026-04-21
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Programação]]

# Entrada e Saída Padrão em C

## Definição

Modelo de texto: sequência de linhas, cada uma terminada em `'\n'`. A biblioteca garante essa aparência independente do SO. Três streams abertos automaticamente: `stdin`, `stdout`, `stderr`.

## Relações (SPO)
- `getchar` → lê → próximo caractere de `stdin`
- `putchar` → escreve → caractere em `stdout`
- `EOF` → sinaliza → fim de arquivo ou erro; definido em `<stdio.h>` (valor típico: `-1`)
- `stdin`/`stdout` → podem ser → redirecionados via `<` e `>` no shell
- Pipe `|` → conecta → `stdout` de um programa ao `stdin` de outro

---

## getchar e putchar

```c
int getchar(void)    /* retorna próximo char ou EOF */
int putchar(int)     /* escreve char; retorna char ou EOF em erro */
```

Frequentemente implementados como macros — sem overhead de chamada de função.

```c
/* lower: converte entrada para minúsculas */
#include <stdio.h>
#include <ctype.h>

main()
{
    int c;
    while ((c = getchar()) != EOF)
        putchar(tolower(c));
    return 0;
}
```

> [!warning] Tipo de `c` deve ser `int`
> `EOF` é valor negativo (`-1`). Se `c` for `char` sem sinal, a comparação com `EOF` nunca será verdadeira em algumas arquiteturas.

---

## Redirecionamento de I/O

O shell redireciona sem que o programa saiba:

| Redireção | Efeito |
|---|---|
| `prog <infile` | `stdin` lido de `infile` |
| `prog >outfile` | `stdout` escrito em `outfile` |
| `otherprog \| prog` | `stdout` de `otherprog` → `stdin` de `prog` |

`"<infile"` **não** aparece em `argv` — consumido pelo shell antes do programa executar.

---

## Saída padrão

`printf` também escreve em `stdout`. Chamadas a `putchar` e `printf` podem ser intercaladas — ordem respeita a sequência das chamadas.

---

## Ver também
- [[printf em C]] — formato, conversões, sprintf
- [[scanf em C]] — entrada formatada
- [[Arquivos em C]] — FILE*, getc/putc, fopen/fclose
- [[Argumentos de Linha de Comando em C]] — argv, argc
- [[Funções em C]] — protótipos, `#include <stdio.h>`
