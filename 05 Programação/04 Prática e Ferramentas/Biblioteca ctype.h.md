---
title: Biblioteca ctype.h
aliases:
  - ctype.h
  - isalpha
  - isdigit
  - tolower
  - toupper
  - classificação de caracteres C
tags:
  - computação/programação/c
date: 2026-04-21
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Programação]]

# Biblioteca ctype.h

## Definição

`<ctype.h>` declara funções para testar e converter caracteres. Todas recebem `int` (valor EOF ou `unsigned char`) e retornam `int`.

Fonte: K&R Appendix B, §B.2.

## Relações (SPO)

| Sujeito | Predicado | Objeto |
|---|---|---|
| funções `is*` | retornam | não-zero (true) se condição satisfeita |
| funções de conversão | retornam | caractere convertido ou `c` inalterado |
| argumento | deve ser | EOF ou `unsigned char` — nunca `char` signed diretamente |

## Funções de teste

| Função | Verdadeiro se `c` é... |
|---|---|
| `isalnum(c)` | letra ou dígito (`isalpha || isdigit`) |
| `isalpha(c)` | letra (`isupper || islower`) |
| `iscntrl(c)` | caractere de controle |
| `isdigit(c)` | dígito decimal `0`–`9` |
| `isgraph(c)` | caractere imprimível exceto espaço |
| `islower(c)` | letra minúscula |
| `isprint(c)` | caractere imprimível incluindo espaço |
| `ispunct(c)` | imprimível exceto espaço, letra ou dígito |
| `isspace(c)` | espaço, `\f`, `\n`, `\r`, `\t`, `\v` |
| `isupper(c)` | letra maiúscula |
| `isxdigit(c)` | dígito hexadecimal (`0`–`9`, `a`–`f`, `A`–`F`) |

Em ASCII 7-bit: imprimíveis = `0x20`–`0x7E`; controles = `0x00`–`0x1F` e `0x7F`.

## Funções de conversão

```c
int tolower(int c);   /* converte maiúscula → minúscula; outros: retorna c */
int toupper(int c);   /* converte minúscula → maiúscula; outros: retorna c */
```

## Exemplos

```c
#include <ctype.h>

/* contar letras e dígitos em string */
int letters = 0, digits = 0;
for (char *p = s; *p; p++) {
    if (isalpha((unsigned char)*p)) letters++;
    if (isdigit((unsigned char)*p)) digits++;
}

/* converter string para minúsculas */
for (char *p = s; *p; p++)
    *p = tolower((unsigned char)*p);
```

> [!warning] Cast para `unsigned char`
> Sempre use `(unsigned char)` ao passar `char` para funções de `<ctype.h>`. `char` signed com valor negativo (caracteres > 127) causa comportamento indefinido.

## Ver também

- [[Tipos de Dados C]] — char, unsigned char
- [[Biblioteca string.h]] — manipulação de strings
- [[Entrada e Saída Padrão em C]] — leitura de caracteres com `getchar`
