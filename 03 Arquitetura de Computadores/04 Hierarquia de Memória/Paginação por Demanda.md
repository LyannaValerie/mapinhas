---
title: Paginação por Demanda
aliases:
  - demand paging
  - falta de página
  - page fault
  - working set
  - conjunto de trabalho
  - thrashing
tags:
  - computação/arquitetura
  - memória
date: 2026-04-21
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Paginação por Demanda

## Definição

Estratégia de [[Memória Virtual]] em que páginas são carregadas da memória secundária para a memória principal **somente quando referenciadas** — não antecipadamente. O programa inicia com todas as entradas da [[Paginação#Tabela de páginas|tabela de páginas]] marcadas como ausentes (P = 0).

## Relações (SPO)

| Sujeito | Predicado | Objeto |
|---|---|---|
| Paginação por demanda | é estratégia de | [[Paginação]] |
| Falta de página | ocorre quando | bit P = 0 na entrada da tabela |
| SO | carrega página | do disco para a RAM ao tratar falta |
| Conjunto de trabalho | modela | localidade de referência do processo |
| Thrashing | ocorre quando | processo gasta mais tempo trocando páginas do que executando |

## Falta de página (page fault)

1. CPU referencia endereço virtual — MMU detecta P = 0
2. MMU gera **trap** para o SO
3. SO verifica se o endereço é válido; se não, segfault
4. SO escolhe quadro de página livre (ou remove uma página — ver [[Política de Substituição de Página]])
5. SO carrega a página do disco no quadro escolhido
6. Tabela de páginas atualizada: P = 1, número do quadro preenchido
7. Instrução reiniciada — execução continua de forma transparente

> [!note] Início sem nada na memória
> Um programa pode iniciar com toda a tabela de páginas marcada ausente. A primeira instrução gera imediatamente uma falta → a página de código é carregada → execução começa. Faltas subsequentes trazem o que for necessário.

## Modelo de conjunto de trabalho

Processos exibem **localidade de referência**: em qualquer intervalo de tempo, acessam apenas um subconjunto pequeno de páginas.

**Conjunto de trabalho** $w(k, t)$: conjunto das $k$ páginas mais recentemente referenciadas pelo processo no instante $t$.

- $k$ muito pequeno → muitas faltas de página
- $k$ muito grande → desperdício de memória

O SO deve garantir que o conjunto de trabalho de cada processo caiba na memória antes de permitir sua execução (*prepaginação*). Se não couber → thrashing.

### Thrashing

Condição em que o sistema passa mais tempo tratando faltas de página do que executando trabalho útil:
- Processo remove uma página que será imediatamente necessária por outro processo
- Ciclos de falta → carregamento → nova falta em cadeia

Solução: limitar o grau de multiprogramação até que a soma dos conjuntos de trabalho caiba na RAM disponível.

## Pré-paginação vs. paginação por demanda

| | Paginação por demanda | Pré-paginação |
|---|---|---|
| Quando carrega | Só ao referenciar | Antecipadamente (prefetch) |
| Faltas de página | Muitas no início | Menos |
| Desperdício de memória | Menor | Maior (pode carregar páginas desnecessárias) |

## Ver também

- [[Paginação]] — mecanismo base
- [[Política de Substituição de Página]] — como escolher qual página remover
- [[Memória Virtual]] — abstração geral
- [[Hierarquia de Memória]] — contexto de custo de acesso a disco
