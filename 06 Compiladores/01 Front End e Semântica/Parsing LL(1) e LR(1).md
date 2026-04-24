---
title: Parsing LL(1) e LR(1)
tags:
  - compiladores
  - programação
  - teoria-da-computação
aliases:
  - LL(1)
  - LR(1)
  - top-down parsing
  - bottom-up parsing
  - shift-reduce
  - recursive descent
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Compiladores]]

# Parsing LL(1) e LR(1)

Duas classes de gramáticas e parsers eficientes para [[Gramática Livre de Contexto (CFG)|CFGs]].

## Hierarquia

```
CFG arbitrárias (Earley: O(n³))
  └─ LR(1) — subset das CFGs não-ambíguas
       └─ LL(1) — subset de LR(1)
            └─ Gramáticas regulares
```

## LL(1)

- **L**eft-to-right scan, **L**eftmost derivation, **1** símbolo de lookahead
- Estratégia **top-down**: expande da raiz para folhas
- Implementações: parser recursivo-descendente (à mão) ou tabela LL(1) (gerado)
- Mais fácil de implementar manualmente
- Menor expressividade: não aceita gramáticas com recursão à esquerda

## LR(1)

- **L**eft-to-right scan, **R**ightmost derivation (reversa), **1** símbolo de lookahead
- Estratégia **bottom-up** (shift-reduce): acumula tokens num stack, reduz quando reconhece handle
- Gerado por ferramentas (yacc, bison, ANTLR)
- Cobre subconjunto maior das CFGs não-ambíguas
- Pré-computa tabelas de parsing → linear na entrada

## Shift-Reduce (LR)

Duas operações:
- **Shift**: empilha próximo token
- **Reduce**: substitui handle no topo do stack pelo não-terminal correspondente

A sequência reversa das reduções é a derivação mais à direita.

## Lookahead

Ambos usam 1 token de lookahead para decidir qual ação tomar sem ambiguidade.

## Escolha prática

> Na prática, poucas construções de linguagens de programação reais ficam no gap entre LR(1) e LL(1). Começar com um gerador de parser disponível é sempre melhor que implementar do zero.

## Algoritmos históricos alternativos

Para CFGs arbitrárias (mais gerais, mas O(n²) ou O(n³)):
- Cocke-Younger-Kasami (CYK)
- Earley's algorithm

Ver [[Parser — Análise Sintática]], [[Gramática Livre de Contexto (CFG)]].
