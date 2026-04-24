---
title: ANSI C — Mudanças
aliases:
  - ANSI C changes
  - K&R C vs ANSI C
  - C89 mudanças
  - C standardization
tags:
  - computação/programação/c
date: 2026-04-21
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Programação]]

# ANSI C — Mudanças

## Definição

Sumário das diferenças entre C da 1ª edição de K&R (1978) e o padrão ANSI C (C89/C90). Quase todas as mudanças são extensões compatíveis com código existente.

Fonte: K&R Appendix C.

## Relações (SPO)

| Sujeito | Predicado | Objeto |
|---|---|---|
| ANSI C | adiciona | protótipos de função com tipos de parâmetro |
| ANSI C | introduz | palavras-chave `const`, `volatile`, `signed`, `void` |
| ANSI C | padroniza | biblioteca padrão (antes dependia de implementação) |
| trigrafos | permitem | representar `#\^[]{}|~` em conjuntos de caracteres limitados |
| linkage externo | limita | a 6 caracteres monocase significativos (mínimo) |

## Mudanças na linguagem

### Palavras-chave novas

- `const` — objeto imutável → [[Qualificadores de Tipo em C]]
- `volatile` — objeto com acesso especial → [[Qualificadores de Tipo em C]]
- `signed` — tipo inteiro com sinal explícito
- `void` — tipo sem valor; `void *` — ponteiro genérico
- `enum` — enumerações (K&R 1ª ed. não tinha formalmente)

> [!info] Palavras não mais reservadas
> `entry` — reservada na 1ª edição mas nunca usada — foi removida.

### Protótipos de função

Maior mudança. ANSI C permite (e recomenda) declarar tipos de parâmetros na declaração:

```c
/* K&R estilo antigo (ainda válido) */
int max(a, b) int a, b; { return a > b ? a : b; }

/* ANSI C — protótipo com tipos */
int max(int a, int b) { return a > b ? a : b; }

/* declaração (protótipo) antes do uso */
int max(int, int);   /* tipos sem nomes são válidos */
```

Protótipos permitem verificação de tipo em chamadas e promoção correta de argumentos.

### Aritmética de ponteiros

- `void *` pode ser comparado e atribuído com qualquer ponteiro de objeto (sem cast explícito em ANSI C; exigia cast em K&R1)

### Caracteres e strings

- Strings adjacentes são concatenadas: `"abc" "def"` → `"abcdef"`
- Trigrafos `??=` → `#`, `??(` → `[`, `??)` → `]`, `??<` → `{`, `??>` → `}`, `??/` → `\`, `??'` → `^`, `??!` → `|`, `??-` → `~`

> [!warning] Trigrafos podem alterar strings!
> `"??="` em string literal → `"#"`. Habilitar trigrafos com `-trigraphs` no GCC.

### Pré-processador

- Operador `#` (stringificação de argumento de macro) — novo
- Operador `##` (concatenação de tokens) — novo → [[Pré-processador C]]
- `#elif` — novo
- `defined` em expressões `#if` — novo

### Conversões

- Regras de promoção aritmética redefinidas: `float op float` permanece `float` (K&R1 promovia para `double`)
- `unsigned` em expressões mistas: regras mais precisas

### Linkage externo

- Mínimo garantido: 6 caracteres monocase significativos (muitas implementações fornecem mais)

## Mudanças na biblioteca

- Biblioteca padrão formalmente padronizada (antes dependia de implementação Unix)
- Novas funções: `strtod`, `strtol`, `strtoul`, `memmove`, `strftime`, `mktime`, etc.
- `<stdarg.h>` substitui `<varargs.h>` (forma portável de listas variáveis)
- `<limits.h>` e `<float.h>` — limites numéricos → [[Limites de Implementação C]]
- `void *` como tipo de retorno de `malloc` (K&R1: `char *`)

## Ver também

- [[Linguagem C]] — visão geral da linguagem
- [[Pré-processador C]] — `#` e `##`, `#elif`, `defined`
- [[Qualificadores de Tipo em C]] — `const`, `volatile`
- [[Listas de Argumentos Variáveis em C]] — `<stdarg.h>` vs `<varargs.h>`
- [[Funções em C]] — protótipos de função
