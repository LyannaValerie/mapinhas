---
title: Codificações de Texto em C
aliases:
  - multibyte C
  - wide character C
  - UTF em C
  - codificação de caracteres C
tags:
  - computação/programação/c
source: Jens Gustedt, Modern C, capítulo 14
date: 2026-04-24
created_by: Codex
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Programação]]

# Codificações de Texto em C

## Definição

C distingue byte, caractere multibyte e caractere largo. Strings comuns são arrays de `char` terminados por nulo; a interpretação dos bytes depende da codificação ativa.

## Relações (SPO)
- string multibyte → termina com → byte nulo
- caractere multibyte → pode ocupar → vários bytes
- caractere multibyte válido → não contém → byte nulo interno
- conversão reiniciável → usa → estado de conversão
- processamento de texto → depende de → locale e codificação

---

## Multibyte

Uma string multibyte continua sendo `char *`, mas um caractere lógico pode ocupar mais de um byte. Portanto, `strlen` mede bytes antes do `'\0'`, não quantidade de caracteres percebidos pelo usuário.

## Conversões reiniciáveis

Funções de conversão reiniciável processam entrada por partes e preservam estado entre chamadas. Isso importa para encodings em que uma sequência pode cruzar fronteiras de buffer.

## Consequência prática

Código que assume "1 byte = 1 caractere" só é correto para subconjuntos simples de codificação. Para texto externo, valide contrato de encoding antes de indexar, cortar ou contar caracteres.

## Ver também

- [[Arrays em C]]
- [[Biblioteca string.h]]
- [[IO de Linhas em C]]

## Nota de integração

criado pelo Codex
