---
title: Parser — Análise Sintática
tags:
  - compiladores
  - programação
aliases:
  - parser
  - análise sintática
  - parsing
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Compiladores]]

# Parser — Análise Sintática

Pass do [[Front End do Compilador]] que verifica se o stream de tokens pertence à linguagem definida pela [[Gramática Livre de Contexto (CFG)|gramática]].

## O que o parser faz

1. Recebe stream de tokens do [[Scanner — Análise Léxica|scanner]]
2. Tenta construir uma **derivação** na gramática
3. Se encontrar: produz árvore sintática
4. Se não encontrar: reporta erro sintático

## Estratégias

### Top-down (descendente)
- Constrói derivação da raiz para as folhas
- Expande o não-terminal mais à esquerda em cada passo
- Implementado como parser recursivo-descendente ou [[Parsing LL(1) e LR(1)#LL(1)|LL(1)]]

### Bottom-up (ascendente)
- Constrói derivação das folhas para a raiz
- Identifica *handles* no stream e reduz para não-terminais
- Implementado como [[Parsing LL(1) e LR(1)#LR(1)|LR(1)]] (shift-reduce)

## Complexidade

Para gramáticas LL(1) e LR(1): tempo linear no tamanho da entrada (O(n)).

## Relação com o scanner

```
Chars → [Scanner] → Tokens → [Parser] → Árvore Sintática → [Type Checker] → IR
```

## Escolha de abordagem

| Abordagem | Quando usar |
|---|---|
| Recursivo-descendente (LL(1)) | gramática pequena, parser escrito à mão |
| LR(1) gerado por ferramenta | gramática complexa, usar gerador (yacc/bison) |

Na prática: LR(1) cobre mais linguagens. LL(1) é mais simples de escrever à mão.
Poucas construções de linguagens de programação reais ficam entre LR(1) e LL(1).

Ver [[Parsing LL(1) e LR(1)]].
