---
title: Biblioteca string.h
aliases:
  - string.h
  - strcpy
  - strcmp
  - strlen
  - strcat
  - memcpy
  - memmove
  - string functions C
tags:
  - computação/programação/c
date: 2026-04-21
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Programação]]

# Biblioteca string.h

## Definição

`<string.h>` declara dois grupos de funções: prefixo `str*` (operam em strings terminadas por `'\0'`) e prefixo `mem*` (operam em blocos de bytes de tamanho `n`).

Fonte: K&R Appendix B, §B.3.

## Relações (SPO)

| Sujeito | Predicado | Objeto |
|---|---|---|
| funções `str*` | assumem | terminador `'\0'` |
| funções `mem*` | operam sobre | `n` bytes sem assumir `'\0'` |
| comparações | tratam argumentos como | arrays `unsigned char` |
| `memmove` | garante | comportamento correto em sobreposição |
| `memcpy` | tem comportamento indefinido em | sobreposição |

## Funções de cópia e concatenação

```c
char *strcpy(s, ct)      /* copia ct → s incluindo '\0'; retorna s */
char *strncpy(s, ct, n)  /* copia até n chars de ct → s; preenche '\0' se ct < n */
char *strcat(s, ct)      /* concatena ct no final de s; retorna s */
char *strncat(s, ct, n)  /* concatena até n chars de ct; termina s com '\0' */
```

> [!warning] Buffer overflow em `strcpy`/`strcat`
> Não verificam tamanho do destino. Prefira `strncpy`/`strncat` com tamanho explícito.

## Funções de comparação

```c
int strcmp(cs, ct)      /* <0 se cs<ct, 0 se igual, >0 se cs>ct */
int strncmp(cs, ct, n)  /* compara até n caracteres */
```

## Funções de busca

```c
char *strchr(cs, c)    /* ponteiro para 1ª ocorrência de c em cs, ou NULL */
char *strrchr(cs, c)   /* ponteiro para última ocorrência de c em cs, ou NULL */

size_t strspn(cs, ct)  /* comprimento do prefixo de cs com chars de ct */
size_t strcspn(cs, ct) /* comprimento do prefixo de cs com chars NÃO em ct */

char *strpbrk(cs, ct)  /* ponteiro para 1ª ocorrência em cs de qualquer char de ct */
char *strstr(cs, ct)   /* ponteiro para 1ª ocorrência da string ct em cs, ou NULL */

size_t strlen(cs)      /* comprimento de cs (exclui '\0') */
char *strerror(n)      /* string de erro correspondente ao errno n */
char *strtok(s, ct)    /* tokeniza s usando delimitadores ct (destrutivo, usa estado estático) */
```

## Funções de memória

```c
void *memcpy(s, ct, n)   /* copia n bytes de ct → s; retorna s (sem sobreposição) */
void *memmove(s, ct, n)  /* como memcpy, mas correto em sobreposição */
int   memcmp(cs, ct, n)  /* compara n bytes; retorno como strcmp */
void *memchr(cs, c, n)   /* ponteiro para 1ª ocorrência de c nos primeiros n bytes de cs */
void *memset(s, c, n)    /* preenche primeiros n bytes de s com char c; retorna s */
```

## Exemplos

```c
#include <string.h>

char dest[100];
strcpy(dest, "hello");
strcat(dest, ", world");     /* dest = "hello, world" */

if (strcmp(a, b) == 0) ...   /* comparação de strings */

char *pos = strstr(haystack, needle);
if (pos) { /* found */ }

memset(buf, 0, sizeof(buf));  /* zera buffer */
memmove(s, s+1, strlen(s));   /* remove 1º char (sobreposição → memmove) */
```

## Ver também

- [[Ponteiros em C]] — strings são `char *`
- [[Arrays em C]] — arrays de char
- [[Codificações de Texto em C]] — multibyte, UTF e contagem de bytes vs caracteres
- [[Biblioteca ctype.h]] — classificação de caracteres individuais
- [[Biblioteca stdlib.h em C]] — `atoi`, `strtol` para conversão de strings em números
