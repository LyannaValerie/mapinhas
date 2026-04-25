---
title: Sequenciamento em C
aliases:
  - sequence points C
  - avaliações sequenciadas em C
  - unsequenced C
tags:
  - computação/programação/c
date: 2026-04-24
source: desconhecida
created_by: "criado pelo Codex"
---

# Sequenciamento em C

Sequenciamento em C define relações de ordem entre avaliações, efeitos colaterais e chamadas de função. A execução textual do programa não implica, sozinha, uma ordem total entre todas as subexpressões.

## Relações (SPO)

- [[Sequenciamento em C]] — restringe — ordem de avaliação em [[Linguagem C]]
- [[Funções em C]] — introduzem — ponto de sequenciamento entre avaliação do chamador e execução da função chamada
- [[Atomicidade e Consistência de Memória em C]] — estende — regras de ordenação para execução concorrente
- [[Sinais em C]] — interrompem — fluxo linear de execução

## Casos centrais

- Argumentos de função podem ser avaliados em ordem não especificada; não dependa de ordem entre eles.
- Expressões não sequenciadas que modificam e leem o mesmo objeto podem gerar comportamento indefinido.
- Chamadas de função são sequenciadas em relação às avaliações do chamador, embora a ordem entre argumentos possa continuar não determinada.
- Inicializadores de arrays e structs podem ter avaliações indeterminadamente sequenciadas.

## Leitura operacional

O ponto prático é separar cálculo de efeito colateral. Se a correção depende de uma ordem entre duas modificações, escreva duas instruções explícitas ou use uma construção que imponha sequenciamento.

## Ver também

- [[Controle de Fluxo C]]
- [[Funções em C]]
- [[Atomicidade e Consistência de Memória em C]]
