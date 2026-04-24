---
title: Performance em C
aliases:
  - desempenho em C
  - otimização C
  - restrict C
  - inline C
tags:
  - computação/fundamentos
source: Jens Gustedt, material didático sobre C23
date: 2026-04-23
created_by: Codex
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Programação]]

# Performance em C

## Definição

Uso de recursos da linguagem e medição empírica para reduzir custo de execução sem violar semântica. Em C, performance depende de layout de memória, aliasing, chamadas de função, IO e comportamento do compilador.

## Relações (SPO)
- `inline` → sugere → expansão de chamada
- `restrict` → declara → ausência de aliasing relevante
- Medição → valida → efeito de otimização
- Inspeção de código gerado → revela → custo real

---

## `inline`

`inline` pode reduzir overhead de chamada, mas não obriga o compilador a expandir. Uso típico: funções pequenas em caminho quente.

## `restrict`

`restrict` informa que, durante o tempo de vida daquele ponteiro, o objeto acessado não será acessado por outro ponteiro incompatível. Isso permite otimizações em loops sobre arrays.

```c
void add(size_t n, double a[restrict n], double b[restrict n]) {
    for (size_t i = 0; i < n; ++i)
        a[i] += b[i];
}
```

## Medição

Otimização sem medição pode piorar programa. Medir antes e depois separa custo real de intuição.

## Nota de integração

criado pelo Codex

