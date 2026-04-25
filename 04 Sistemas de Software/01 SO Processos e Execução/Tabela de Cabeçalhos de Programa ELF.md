---
title: Tabela de Cabeçalhos de Programa ELF
aliases:
  - program header table
  - PHT
  - segmentos ELF
tags:
  - computação/sistemas
source: "Tu, Do Hoang. Operating Systems: From 0 to 1"
created_by: Codex
---

# Tabela de Cabeçalhos de Programa ELF

%% criado pelo Codex %%

## Definição

Array de cabeçalhos de programa que define o layout de memória de um executável em tempo de execução.

## Papel

Cada cabeçalho descreve um segmento. Um segmento reúne zero ou mais seções, mas o [[Carregador]] usa segmentos, não seções, para carregar o programa na [[Memória]].

Tipos comuns incluem `PHDR`, `INTERP`, `LOAD`, `DYNAMIC`, `NOTE`, `TLS` e `GNU_STACK`.

## Relações

- Inspecionada por [[Readelf]] com `readelf -l`.
- Complementa a [[Tabela de Cabeçalhos de Seção ELF]].
- Conecta [[Formato ELF]] ao carregamento real do processo.

