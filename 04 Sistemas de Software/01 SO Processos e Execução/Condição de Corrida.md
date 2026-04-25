---
title: Condição de Corrida
aliases:
  - race condition
  - corrida crítica
tags:
  - computação/sistemas
source: "Sistemas Operacionais Modernos, cap. 2"
created_by: Codex
---

# Condição de Corrida

Nota criada pelo Codex.

## Definição

Erro de concorrência em que o resultado depende da ordem temporal de execução entre [[Processo|processos]] ou [[Thread|threads]].

## Mecanismo

A condição aparece quando entidades concorrentes acessam estado compartilhado e ao menos uma delas escreve nesse estado. Sem coordenação, uma interleaving diferente pode sobrescrever dados, observar valor parcial ou violar invariante.

## Relações (SPO)

- Condição de Corrida → surge em → estado compartilhado
- Condição de Corrida → depende de → ordem de escalonamento
- Condição de Corrida → evitada por → [[Exclusão Mútua]]
- Condição de Corrida → ocorre dentro de → [[Região Crítica]]
- [[Thread]] → pode causar → Condição de Corrida

