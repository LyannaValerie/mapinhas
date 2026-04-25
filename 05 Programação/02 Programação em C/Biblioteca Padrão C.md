---
title: Biblioteca Padrão C
aliases:
  - C standard library
  - biblioteca standard C
  - runtime library C
  - biblioteca de runtime C
tags:
  - computação/programação/c
date: 2026-04-24
source: fonte bibliográfica não confirmada
---

%% Nota criada pelo Codex %%
%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Programação]]

# Biblioteca Padrão C

## Definição
Conjunto padronizado de funções e headers que fornece serviços não embutidos diretamente na [[Linguagem C]], como entrada e saída, manipulação de strings, memória dinâmica, matemática e utilidades de runtime.

## Relações (SPO)
- [[Linguagem C]] → mantém fora do compilador → várias operações de runtime
- Biblioteca padrão → fornece → [[Entrada e Saída Padrão em C]]
- Biblioteca padrão → fornece → [[Alocação Dinâmica de Memória em C]]
- Biblioteca padrão → padronizada por → ANSI C
- [[ANSI C — Mudanças]] → formaliza → biblioteca padrão

## Papel no desenho de C
C deixa várias responsabilidades explícitas para o programador e para bibliotecas. Em vez de inserir automaticamente suporte de runtime para tudo, o compilador preserva uma linguagem pequena e delega operações para chamadas explícitas.

Exemplos de responsabilidades deixadas ao código ou à biblioteca:
- gerenciamento dinâmico de memória
- entrada e saída
- checagem de limites, quando necessária
- manipulação de strings
- conversões e utilitários numéricos

## Entrada e saída
I/O não fazia parte do núcleo da linguagem C. O suporte prático veio por rotinas de biblioteca, depois consolidadas como entrada e saída padrão.

## Padronização
Antes da padronização, a biblioteca dependia fortemente de prática de implementação. O ANSI C formalizou a biblioteca como parte do ambiente padronizado, reduzindo dependência de convenções locais.

## Ver também
- [[Entrada e Saída Padrão em C]]
- [[Arquivos em C]]
- [[Biblioteca stdlib.h em C]]
- [[Biblioteca string.h]]
- [[Biblioteca ctype.h]]
- [[Biblioteca math.h]]
- [[ANSI C — Mudanças]]
