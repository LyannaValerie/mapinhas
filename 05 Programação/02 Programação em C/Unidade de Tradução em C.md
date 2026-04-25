---
title: Unidade de Tradução em C
aliases:
  - translation unit C
  - unidade de compilação C
  - arquivo fonte C
tags:
  - computação/programação/c
date: 2026-04-24
source: Kernighan e Ritchie, The C Programming Language, 2nd ed., Appendix A
---

%% Nota criada pelo Codex %%
%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Programação]]

# Unidade de Tradução em C

## Definição
Unidade de entrada que o compilador C traduz após as fases de preprocessamento. Uma unidade de tradução resulta de um arquivo fonte com seus headers incluídos e diretivas processadas.

## Relações (SPO)
- Arquivo fonte C → forma base de → unidade de tradução
- [[Pré-processador C]] → transforma → arquivo fonte em sequência de tokens
- Unidade de tradução → contém → declarações externas
- Declarações externas → definem ou declaram → funções e objetos
- Linker → combina → múltiplas unidades de tradução

## Fases relevantes
Antes da análise sintática da linguagem propriamente dita, ocorrem transformações lexicais e de preprocessamento:
- inclusão de arquivos
- expansão de macros
- compilação condicional
- redução do programa a uma sequência de tokens

## Tokens
Depois do preprocessamento, o programa é tratado como sequência de tokens. Classes relevantes:
- identificadores
- palavras-chave
- constantes
- literais de string
- operadores
- separadores

## Escopo de compilação
Cada unidade de tradução é compilada separadamente. Comunicação entre arquivos ocorre por declarações, linkage e headers compartilhados.

## Ver também
- [[Pré-processador C]]
- [[Escopo em C]]
- [[Funções em C]]
- [[Variáveis Estáticas em C]]
- [[Arquivos em C]]
- [[Sistema de Compilação]]
