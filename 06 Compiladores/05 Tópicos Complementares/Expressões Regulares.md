---
title: Expressões Regulares
tags:
  - compiladores
  - programação
  - teoria-da-computação
aliases:
  - RE
  - regular expression
  - regex
  - linguagem regular
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Compiladores]]

# Expressões Regulares

Notação concisa para especificar linguagens regulares — usadas para definir a estrutura léxica de linguagens de programação.

## Definição

A linguagem descrita por uma RE é chamada **linguagem regular**.

Equivalência fundamental:
> **RE ↔ [[Autômato Finito (FA)|FA]]** — todo FA tem uma RE equivalente e vice-versa.

## Operadores básicos

| Operação | Notação | Significado |
|---|---|---|
| Concatenação | `ab` | `a` seguido de `b` |
| Alternação | `a\|b` | `a` ou `b` |
| Kleene star | `a*` | zero ou mais repetições de `a` |
| Parênteses | `(a\|b)c` | agrupamento |

## Exemplos práticos

```
new              — palavra literal "new"
new|not          — "new" ou "not"
[0-9]+           — um ou mais dígitos
[a-zA-Z][a-zA-Z0-9]*   — identificador
\{[^}]*\}        — comentário Pascal: { ... }
```

## Por que usar RE no lugar de FA direto

- FA é preciso mas não é intuitivo para humanos escreverem
- RE é compacta e legível
- Ferramentas como `lex`/`flex` convertem RE em FA automaticamente

## Construção RE → FA

1. RE → NFA (Thompson's construction)
2. NFA → DFA (subset construction)
3. DFA → código do scanner

## Relação com outras estruturas

- RE descreve linguagens regulares (nível mais baixo da hierarquia de Chomsky)
- [[Gramática Livre de Contexto (CFG)]] descreve linguagens context-free (nível acima)
- O [[Scanner — Análise Léxica|scanner]] usa RE/FA; o [[Parser — Análise Sintática|parser]] usa CFG
