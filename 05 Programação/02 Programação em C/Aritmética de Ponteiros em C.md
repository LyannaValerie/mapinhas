---
title: Aritmética de Ponteiros em C
aliases:
  - pointer arithmetic C
  - diferença de ponteiros
  - ptrdiff_t
tags:
  - computação/programação/c
source: Jens Gustedt, Modern C, capítulos 11 e 15
date: 2026-04-24
created_by: Codex
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Programação]]

# Aritmética de Ponteiros em C

## Definição

Aritmética de ponteiros é deslocamento por objetos, não por bytes. Em `T *p`, `p + n` aponta para o n-ésimo objeto `T` depois de `p`.

## Relações (SPO)
- `p + n` → desloca → `n * sizeof *p` bytes conceituais
- `p - q` → produz → distância em elementos
- diferença de ponteiros → tem tipo → `ptrdiff_t`
- aritmética válida → fica dentro de → mesmo array ou posição um-após-o-fim
- aritmética inválida → pode causar → comportamento indefinido

---

## Regra de domínio

Somar, subtrair e comparar ponteiros só é definido quando os ponteiros pertencem ao mesmo array, incluindo a posição um-após-o-fim. Essa posição pode ser calculada e comparada, mas não derreferenciada.

```c
for (double *p = a; p < a + len; ++p)
    sum += *p;
```

## Diferença de posições

`p - q` mede quantos elementos separam dois ponteiros compatíveis do mesmo array. Use `ptrdiff_t`, não `int` ou `size_t`, porque a diferença pode ser negativa.

```c
ptrdiff_t n = end - begin;
```

## Preferência prática

Quando possível, prefira indexação de array em código comum. Aritmética explícita de ponteiros é útil, mas aumenta risco de sair do domínio válido do objeto.

## Ver também

- [[Ponteiros em C]]
- [[Arrays em C]]
- [[Falhas em Programas C]]

## Nota de integração

criado pelo Codex
