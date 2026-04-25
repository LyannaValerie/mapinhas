---
title: Ponteiro Nulo em C
aliases:
  - null pointer C
  - NULL em C
  - nullptr em C
tags:
  - computação/programação/c
source: Jens Gustedt, Modern C, capítulo 11
date: 2026-04-24
created_by: Codex
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Programação]]

# Ponteiro Nulo em C

## Definição

Ponteiro nulo é valor especial de ponteiro que não aponta para nenhum objeto ou função válido. Serve como sentinela para ausência de endereço, falha de busca, fim de lista ou falha de alocação.

## Relações (SPO)
- ponteiro nulo → representa → ausência de objeto apontado
- teste `if (p)` → verifica → ponteiro não nulo
- `NULL` → é → macro histórica para constante de ponteiro nulo
- `nullptr` → é → constante de ponteiro nulo em C23
- derreferenciar ponteiro nulo → causa → comportamento indefinido

---

## Uso

```c
int *p = nullptr;

if (p) {
    use(*p);
}
```

Em código pré-C23, `NULL` ou `0` aparecem com a mesma função semântica. Em código C23, `nullptr` expressa melhor a intenção.

## Estado de ponteiro

Ponteiros têm valor lógico: nulo é falso; não nulo é verdadeiro. Após liberar ou invalidar um ponteiro variável, atribuir valor nulo reduz chance de reuso acidental.

```c
free(p);
p = nullptr;
```

Isso não invalida cópias antigas do mesmo endereço.

## Ver também

- [[Ponteiros em C]]
- [[Alocação Dinâmica de Memória em C]]
- [[Falhas em Programas C]]

## Nota de integração

criado pelo Codex
