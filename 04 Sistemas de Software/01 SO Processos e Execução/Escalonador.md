---
title: Escalonador
aliases:
  - scheduler
  - escalonador de CPU
tags:
  - computação/sistemas
source: "Sistemas Operacionais Modernos, cap. 2"
created_by: Codex
---

# Escalonador

Nota criada pelo Codex.

## Definição

Componente do [[Kernel]] que aplica uma política de [[Escalonamento de CPU]] para escolher a próxima entidade executável.

## Papel técnico

O escalonador opera sobre filas de processos ou threads prontos. Ele precisa balancear metas que podem conflitar, como maximizar uso de CPU, reduzir tempo de resposta e evitar starvation.

## Relações (SPO)

- Escalonador → implementa → [[Escalonamento de CPU]]
- Escalonador → escolhe → [[Processo]] pronto
- Escalonador → escolhe → [[Thread]] pronta
- Escalonador → executa em → [[Kernel]]
- Escalonador → influencia → [[Condição de Corrida|ordem observada de execução]]

