---
title: Gramática ANSI C
aliases:
  - grammar ANSI C
  - gramática C89
  - gramática da linguagem C
tags:
  - computação/programação/c
  - computação/compiladores
date: 2026-04-24
source: Kernighan e Ritchie, The C Programming Language, 2nd ed., Appendix A
---

%% Nota criada pelo Codex %%
%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Programação]]

# Gramática ANSI C

## Definição
Descrição formal da sintaxe de C conforme o manual de referência associado ao ANSI C. A gramática organiza como tokens formam expressões, declarações, comandos e unidades de tradução.

## Relações (SPO)
- [[Unidade de Tradução em C]] → é composta por → declarações externas
- Declaração externa → declara ou define → função ou objeto
- Expressão C → combina → operadores e operandos
- Declaração C → combina → especificadores e declaradores
- Gramática ANSI C → pode alimentar → geradores de parser
- Ambiguidade `if-else` → gera → conflito clássico de parsing

## Estrutura geral
```text
translation-unit
  -> external-declaration
  -> translation-unit external-declaration
```

A unidade de tradução é o nível sintático superior. Abaixo dela aparecem definições de função, declarações de objetos e estruturas sintáticas menores.

## Núcleos sintáticos
- expressões
- declarações
- comandos
- definições externas
- diretivas de preprocessamento

## Relação com compiladores
A gramática de C é material de entrada para [[Parser — Análise Sintática]]. Com uma adaptação, pode ser convertida para formato aceito por geradores de parser como YACC, preservando o problema clássico do `else` associado ao `if` mais próximo.

## Ver também
- [[Parser — Análise Sintática]]
- [[Gramática Livre de Contexto (CFG)]]
- [[Pré-processador C]]
- [[Unidade de Tradução em C]]
- [[Operadores C]]
- [[Funções em C]]
