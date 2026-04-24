---
title: Gramática Livre de Contexto (CFG)
tags:
  - compiladores
  - programação
  - teoria-da-computação
aliases:
  - CFG
  - context-free grammar
  - gramática livre de contexto
  - gramática
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Compiladores]]

# Gramática Livre de Contexto (CFG)

Formalismo matemático que define a sintaxe de linguagens de programação. Usada pelo [[Parser — Análise Sintática|parser]].

## Definição

Conjunto finito de regras (*produções*) que definem um conjunto (possivelmente infinito) de strings válidas.

Componentes:
- **Terminais**: tokens concretos (saem do [[Scanner — Análise Léxica|scanner]])
- **Não-terminais** (variáveis sintáticas): categorias abstratas
- **Produções**: regras de reescrita `Não-terminal → sequência`
- **Símbolo inicial**: não-terminal raiz

## Exemplo

```
1. Sentence → Subject verb Object endmark
2. Subject  → noun
3. Subject  → noun Modifier
4. Object   → noun
5. Object   → noun Modifier
6. Modifier → adjective
```

`Sentence`, `Subject`, `Object`, `Modifier` são não-terminais.
`noun`, `verb`, `adjective`, `endmark` são terminais (categorias sintáticas).

## Derivação

Sequência de substituições de não-terminais pelas suas produções até obter só terminais.

- **Derivação mais à esquerda** (*leftmost*): sempre reescreve o não-terminal mais à esquerda
- **Derivação mais à direita** (*rightmost*): sempre reescreve o mais à direita

## Árvore sintática (parse tree)

Representação da derivação como grafo. Codifica a estrutura hierárquica da sentença.

## Ambiguidade

Uma CFG é **ambígua** se alguma sentença tem mais de uma árvore sintática (= mais de uma derivação mais à esquerda). Ambiguidade causa problemas para o compilador.

## Hierarquia de classes

```
CFG arbitrárias
  └─ LR(1) grammars  ← subconjunto das CFGs não-ambíguas
       └─ LL(1) grammars
            └─ Gramáticas regulares (RG) ↔ FA ↔ RE
```

Ver [[Parsing LL(1) e LR(1)]].
