---
title: Cabeçalho ELF
aliases:
  - ELF header
  - cabeçalho Executable and Linkable Format
tags:
  - computação/sistemas
source: "Tu, Do Hoang. Operating Systems: From 0 to 1"
created_by: Codex
---

# Cabeçalho ELF

%% criado pelo Codex %%

## Definição

Primeira estrutura de um arquivo [[Formato ELF]]. Descreve a organização do binário e fornece metadados para ferramentas, ligador e carregador.

## Papel

O cabeçalho aponta para tabelas internas do arquivo, como a [[Tabela de Cabeçalhos de Seção ELF]] e a [[Tabela de Cabeçalhos de Programa ELF]]. Sem esse metadado inicial, o sistema não sabe interpretar corretamente o restante do executável.

## Relações

- Inspecionado por [[Readelf]] com `readelf -h`.
- Usado pelo [[Carregador]] para interpretar executáveis.
- Parte estrutural de [[Arquivo Objeto]] e executáveis ELF.

