---
title: Escalonamento de CPU
aliases:
  - escalonamento
  - CPU scheduling
  - escalonamento de processos
tags:
  - computação/sistemas
source: "Sistemas Operacionais Modernos, cap. 2"
created_by: Codex
---

# Escalonamento de CPU

Nota criada pelo Codex.

## Definição

Decisão do [[Sistema Operacional]] sobre qual [[Processo]] ou [[Thread]] deve usar o [[Processador]] em seguida.

## Papel técnico

O escalonamento distribui tempo de CPU entre entidades prontas para executar. A política escolhida afeta vazão, latência, responsividade, justiça e previsibilidade.

## Relações (SPO)

- Escalonamento de CPU → seleciona → próximo [[Processo]]
- Escalonamento de CPU → seleciona → próxima [[Thread]]
- Escalonamento de CPU → executado por → [[Escalonador]]
- Escalonamento de CPU → depende de → política de escalonamento
- [[Multitarefa]] → requer → Escalonamento de CPU

