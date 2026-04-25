---
title: Ponteiros para Função em C
aliases:
  - function pointer C
  - callback em C
  - função de comparação C
tags:
  - computação/programação/c
source: Jens Gustedt, Modern C, capítulo 11
date: 2026-04-24
created_by: Codex
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Programação]]

# Ponteiros para Função em C

## Definição

Ponteiro para função armazena referência chamável para uma função com assinatura específica. Permite parametrizar comportamento sem duplicar algoritmo.

## Relações (SPO)
- nome de função sem chamada → decai para → ponteiro para função
- ponteiro para função → exige → assinatura compatível
- callback → injeta → política em algoritmo genérico
- `qsort` e `bsearch` → recebem → função de comparação
- chamada com protótipo incompatível → pode causar → comportamento indefinido

---

## Declaração

```c
int (*compar)(void const *, void const *);
```

Forma com `typedef` reduz ruído quando a assinatura aparece em várias interfaces:

```c
typedef int compare_function(void const *, void const *);
compare_function *compar;
```

## Uso em interfaces genéricas

`qsort` e `bsearch` combinam `void *`, tamanho de elemento e callback de comparação. O algoritmo não conhece o tipo concreto; a função de comparação restaura a semântica do domínio.

```c
int compare_unsigned(void const *a, void const *b);
```

## Ver também

- [[Funções em C]]
- [[Ponteiro void em C]]
- [[Biblioteca stdlib.h em C]]
- [[Programação Genérica em C]]

## Nota de integração

criado pelo Codex
