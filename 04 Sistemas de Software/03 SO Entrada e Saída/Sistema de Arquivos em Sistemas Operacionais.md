---
title: Sistema de Arquivos em Sistemas Operacionais
aliases:
  - file system em SO
  - sistema de arquivos do sistema operacional
  - arquivos em sistemas operacionais
tags:
  - computação/sistemas
  - computação/armazenamento
source: "Sistemas Operacionais Modernos, cap. 1"
created_by: Codex
date: 2026-04-25
---

%% criado pelo Codex %%

# Sistema de Arquivos em Sistemas Operacionais

## Definição

Abstração do [[Sistema Operacional]] que organiza dados persistentes como arquivos nomeados, diretórios e metadados.

## Relações (SPO)

- Sistema de Arquivos em Sistemas Operacionais -> organiza -> arquivos
- Diretório -> mapeia -> nome para arquivo
- Caminho absoluto -> parte de -> raiz
- Caminho relativo -> parte de -> diretório corrente
- Arquivo -> pode representar -> dados persistentes
- [[Arquivo Especial]] -> representa -> dispositivo
- [[Chamada do Sistema]] -> acessa -> arquivos

## Papel técnico

O sistema de arquivos substitui a visão bruta de blocos de disco por nomes, diretórios, permissões e operações como abrir, ler, escrever e remover.

## Caminhos

Em sistemas UNIX, caminhos absolutos começam na raiz `/`; caminhos relativos são interpretados a partir do diretório corrente do processo.
