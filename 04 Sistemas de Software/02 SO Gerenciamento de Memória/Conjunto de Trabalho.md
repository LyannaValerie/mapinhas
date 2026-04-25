---
title: Conjunto de Trabalho
aliases:
  - working set
tags:
  - computação/sistemas
  - sistemas-operacionais/memória
source: "Sistemas Operacionais Modernos, cap. 3"
created_by: Codex
---

# Conjunto de Trabalho

Nota criada pelo Codex.

## Definição

Conjunto de páginas que um [[Processo]] usou recentemente e tende a precisar para continuar executando com baixa taxa de faltas.

## Papel técnico

O modelo de conjunto de trabalho explora localidade de referência. Se a RAM disponível para um processo não comporta seu conjunto ativo, ele passa a gerar muitas [[Falha de Página|falhas de página]] e perde tempo esperando E/S.

## Funcionamento

**Localidade de referência:** programas não referenciam páginas uniformemente. Em um dado instante, um processo usa intensamente um subconjunto pequeno de suas páginas (loops, arrays ativos, código corrente).

**Definição formal:** W(k, t) = conjunto de páginas distintas referenciadas nas últimas *k* referências de memória até o instante *t*.

**Implicação prática:** se o SO mantém o conjunto de trabalho de cada processo na RAM, a taxa de [[Falha de Página|falhas de página]] cai drasticamente. Se os quadros disponíveis são insuficientes para o conjunto de trabalho → o processo gera falhas contínuas → [[Thrashing]].

**Política de pré-paginação (prepaging):** ao trazer um processo do disco, carrega seu conjunto de trabalho inteiro antes de retomar execução — evita rajada de falhas logo após o retorno.

**WSClock** usa o conceito de conjunto de trabalho para decidir substituição: páginas referenciadas recentemente (dentro de uma janela de tempo τ) fazem parte do conjunto de trabalho e não devem ser removidas.

## Relações (SPO)

- Conjunto de Trabalho -> representa -> localidade atual do processo
- Conjunto de Trabalho -> orienta -> [[Algoritmo de Substituição de Páginas]]
- conjunto de trabalho maior que quadros disponíveis -> causa -> [[Thrashing]]
- [[Memória Virtual em Sistemas Operacionais]] -> depende de -> localidade de referência

