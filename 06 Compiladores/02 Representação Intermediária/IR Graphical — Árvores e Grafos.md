---
title: IR Graphical — Árvores e Grafos
tags:
  - compiladores
  - ir
aliases:
  - graphical IR
  - IR grafical
  - AST
  - DAG
  - parse tree
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Compiladores]]

# IR Graphical — Árvores e Grafos

IRs que codificam o conhecimento do compilador em forma de grafo. Algoritmos operam sobre nós, arestas, listas e árvores.

> [!info] Contexto
> Seção 5.2 de *Engineering a Compiler*. Parte do cap. 5 sobre [[Representação Intermediária (IR)]].

## Parse Tree

Representação gráfica da **derivação** completa. Um nó para cada símbolo gramatical na derivação.

- Vantagem: captura toda estrutura da análise sintática
- Desvantagem: grande, com muitos nós de não-terminais sem valor semântico
- Uso: sistemas de gramáticas de atributos; raramente em compiladores modernos

## Abstract Syntax Tree (AST)

Contração do parse tree que **elimina nós extrâneos** (não-terminais intermediários que não carregam informação).

```
a × 2 + a × 2 × b  →  AST:
         +
        / \
       ×   ×
      /\ / \
     a 2 a  ×
           / \
          2   b
```

- Representação **próxima do código fonte**
- O parser pode construir a AST diretamente (via [[Tradução Dirigida por Sintaxe — Ad Hoc]])
- Usada em sistemas source-to-source, S-expressions em Lisp/Scheme

### Trade-off de Abstração

| Nível | Vantagem | Desvantagem |
|---|---|---|
| Alto (source-level) | fácil identificar construtos (ex: array ref) | otimizações de máquina ficam implícitas |
| Baixo (near-machine) | expõe detalhes de endereçamento | perde estrutura semântica |

## DAG — Directed Acyclic Graph

AST onde **subexpressões comuns** são compartilhadas (compartilhamento de nós).

- Representa o mesmo valor computado uma única vez
- Permite identificar expressões comuns automaticamente
- Mais compacto que a AST

```
a × 2 + a × 2 × b:
         +
        / \
       ×   ×
      /|   |\
     a 2   | b
           ↑
     (mesmo nó ×)
```

## Grafos de Dependência de Dados

Grafo dirigido onde:
- **Nós** = operações
- **Arestas** = dependências de dados entre operações

Expõe paralelismo: operações sem caminho entre si podem executar em paralelo.

## CFG — Control-Flow Graph

Grafo dirigido onde:
- **Nós** = blocos básicos (sequências de código sem desvio interno)
- **Arestas** = transferências de controle possíveis

> [!note] Definição
> **Bloco básico**: sequência máxima de operações sem desvio, exceto possivelmente ao final.

O CFG é construído sobre IR linear (ver [[IR Linear — Código de Três Endereços]]).

## Hybrid IRs

Combinam árvores e IR linear. Ex.: muitos compiladores C usam árvores de expressão de baixo nível com operações de controle lineares.

## Relações

- [[Representação Intermediária (IR)]] — overview geral
- [[IR Linear — Código de Três Endereços]] — alternativa linear
- [[Forma SSA]] — naming scheme que complementa IRs lineares
- [[Gramáticas de Atributos]] — parse tree como IR primária
- [[Tradução Dirigida por Sintaxe — Ad Hoc]] — constrói AST durante parsing
