---
title: Scanner — Análise Léxica
tags:
  - compiladores
  - programação
aliases:
  - scanner
  - lexical analysis
  - análise léxica
  - tokenizer
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Compiladores]]

# Scanner — Análise Léxica

Pass do [[Front End do Compilador]] que converte sequência de caracteres em sequência de **tokens** classificados.

## Token

Par `(categoria sintática, lexema)`.

- **Categoria sintática** (part of speech): `noun`, `verb`, `integer`, `identifier`, `keyword`, etc.
- **Lexema**: o texto real — ex: `"Compilers"`, `113`

Exemplo: a sentença `"Compilers are engineered objects."` vira:
```
(noun,"Compilers"), (verb,"are"), (adjective,"engineered"),
(noun,"objects"), (endmark,".")
```

Na prática, o lexema é armazenado numa tabela hash e representado por índice inteiro.

## Autômato Finito

Scanner é implementado como [[Autômato Finito (FA)]].

- Diagrama de transição descreve a linguagem léxica
- Conjuntos finitos de palavras → diagramas acíclicos
- Conjuntos infinitos (inteiros, identificadores) → diagramas cíclicos (laços)

## Expressões Regulares

[[Expressões Regulares]] são a notação concisa para especificar estrutura léxica.
- Equivalentes a FA (provado por construção)
- Simples de escrever, convertíveis em FA automaticamente

## Relação com o parser

Scanner produz o stream de tokens que o [[Parser — Análise Sintática|parser]] consome para verificar sintaxe.

```
Chars → [Scanner] → Tokens → [Parser] → Árvore Sintática
```
