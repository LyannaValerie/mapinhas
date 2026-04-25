---
title: Macros Tipo Função em C
aliases:
  - function-like macros C
  - macros com argumentos C
  - macro tipo função C
tags:
  - computação/fundamentos
source: Jens Gustedt, material didático sobre C23
date: 2026-04-23
created_by: Codex
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Programação]]

# Macros Tipo Função em C

## Definição

Macro do pré-processador que recebe argumentos e expande tokens antes da compilação. Parece função no uso, mas não possui chamada, tipo, escopo de parâmetro nem avaliação única garantida.

## Relações (SPO)
- Macro tipo função → expande → tokens
- Argumento de macro → pode ser avaliado → múltiplas vezes
- `#` → transforma → argumento em string
- `##` → concatena → tokens
- `__VA_ARGS__` → representa → argumentos variáveis

---

## Risco principal

```c
#define MAX(a, b) ((a) > (b) ? (a) : (b))
```

`MAX(i++, j++)` pode incrementar argumento mais de uma vez. Macro não tem semântica de função.

## Checagem de argumentos

Quando possível, prefira função `static inline` para preservar tipo e avaliação única. Macro continua útil para geração de código, seleção genérica e integração com pré-processador.

Macros tipo função permanecem relevantes em C porque viabilizam interfaces fáceis para [[Programação Genérica em C]] e checagens de parâmetros que precisam ocorrer antes da compilação semântica.

## Relação com pré-processador

Esta nota detalha caso específico de [[Pré-processador C]]. Para constantes, `#include`, `#if`, `#undef`, stringificação e concatenação básica, ver nota principal.

## Ver também

- [[Funções Inline em C]]
- [[Programação Genérica em C]]
- [[Performance em C]]

## Nota de integração

criado pelo Codex
