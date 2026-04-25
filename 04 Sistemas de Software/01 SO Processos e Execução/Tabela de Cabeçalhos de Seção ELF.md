---
title: Tabela de Cabeçalhos de Seção ELF
aliases:
  - section header table
  - SHT
  - seções ELF
tags:
  - computação/sistemas
source: "Tu, Do Hoang. Operating Systems: From 0 to 1"
created_by: Codex
---

# Tabela de Cabeçalhos de Seção ELF

%% criado pelo Codex %%

## Definição

Array de estruturas de tamanho fixo que descreve as seções de um arquivo [[Formato ELF]].

## Papel

Seções agrupam conteúdo de propósito específico, como código, dados, strings e símbolos. Elas são importantes para ferramentas de análise, montagem, linkedição e depuração.

## Relações

- Inspecionada por [[Readelf]] com `readelf -S`.
- Diferente de [[Tabela de Cabeçalhos de Programa ELF|segmentos]], que orientam carregamento em memória.
- Relaciona-se com [[Símbolos de Ligação]] e [[Relocação de Código]].

