---
title: Política de Substituição de Página
aliases:
  - page replacement
  - substituição de página
  - NRU
  - FIFO
  - LRU
  - Second Chance
  - Clock
tags:
  - computação/arquitetura
  - memória
date: 2026-04-21
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Política de Substituição de Página

## Definição

Algoritmo usado pelo SO para decidir **qual página remover** da memória principal quando ocorre uma [[Paginação por Demanda#Falta de página (page fault)|falta de página]] e não há quadros livres. O objetivo é minimizar faltas de página futuras.

Cada entrada da [[Paginação#Tabela de páginas|tabela de páginas]] tem dois bits de hardware:

- **Bit R** (Referenced): setado pela MMU a cada leitura ou escrita na página; pode ser zerado pelo SO periodicamente
- **Bit M** (Modified / dirty): setado pela MMU quando a página é escrita; só zerado ao gravar a página no disco

## Algoritmos

### NRU — Not Recently Used

Divide páginas em 4 classes pelo estado dos bits R e M:

| Classe | R | M | Prioridade de remoção |
|---|---|---|---|
| 0 | 0 | 0 | Primeira (melhor candidata) |
| 1 | 0 | 1 | Segunda |
| 2 | 1 | 0 | Terceira |
| 3 | 1 | 1 | Última (mais custosa) |

Remove uma página aleatória da menor classe não vazia. O SO zera periodicamente todos os bits R (a cada interrupt de clock) para distinguir páginas usadas recentemente das usadas há mais tempo.

> [!note] Preferência
> Prefere remover página não modificada (M=0) para evitar escrita no disco. Página não referenciada (R=0) é menos ativa.

### FIFO — First In, First Out

Remove a página carregada há mais tempo. Simples de implementar (fila circular).

**Problema:** pode remover páginas muito usadas que simplesmente foram carregadas cedo. Sofre de **Anomalia de Bélády**: com mais quadros, pode ocorrer *mais* faltas de página.

### Second Chance (Clock)

Variante do FIFO que dá uma segunda chance às páginas com R = 1:

1. Examina a página mais antiga (cabeça da fila)
2. Se R = 0: remove a página
3. Se R = 1: zera R, move a página para o fim da fila, avança ao próximo

Implementado eficientemente como lista circular (algoritmo do relógio / Clock).

### LRU — Least Recently Used

Remove a página que **não foi usada há mais tempo**. Aproxima o comportamento ótimo (OPT).

Implementação exata é cara: requer atualizar uma lista ordenada a cada referência de memória. Soluções práticas:

- **Contador de hardware**: registra o número do ciclo do último acesso; remove a de menor contador
- **Matriz de bits**: matriz n×n; a cada acesso à página *i*, linha *i* toda para 1, coluna *i* toda para 0; página menos usada = linha com menor valor binário

### Algoritmo Ótimo (OPT)

Remove a página que **será referenciada mais tarde no futuro**. Impossível de implementar em tempo real (exigiria conhecer o futuro); serve como limite teórico para comparação.

## Comparação

| Algoritmo | Implementação | Anomalia de Bélády | Qualidade |
|---|---|---|---|
| OPT | Impossível | Não | Ótimo (referência) |
| LRU | Cara | Não | Boa |
| NRU | Simples | Não | Razoável |
| Second Chance | Moderada | Não | Boa |
| FIFO | Trivial | Sim | Fraca |

## Ver também

- [[Paginação por Demanda]] — contexto de quando a política é invocada
- [[Paginação]] — estrutura da tabela de páginas (bits R e M)
- [[Cache]] — analogia com políticas de substituição de cache
