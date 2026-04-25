---
title: Escalonamento no Linux
aliases:
  - escalonador Linux
  - Linux scheduler
  - escalonamento de processos no Linux
  - CFS
tags:
  - computação/sistemas
source: "Sistemas Operacionais Modernos, cap. 10"
created_by: Codex
---

# Escalonamento no Linux

## Definição

Política e mecanismo do núcleo Linux para escolher qual tarefa executa em cada CPU. Escalonamento opera sobre **threads de núcleo** (não processos).

## Papel técnico

O escalonador equilibra responsividade interativa, vazão, justiça, afinidade de cache e paralelismo em múltiplos processadores. Em sistemas multicore, decide também "em qual CPU executa".

## Relações (SPO)

- Escalonamento no Linux -> implementa -> [[Escalonamento de CPU]]
- Escalonador Linux -> escolhe -> próxima tarefa executável
- CFS -> ordena tarefas por -> `vruntime` mínimo
- Afinidade de CPU -> reduz -> perda de localidade de cache
- Balanceamento de carga -> redistribui -> tarefas entre CPUs
- Prioridade/`nice` -> influencia -> taxa de crescimento de `vruntime`

## 3 Classes de Threads

| Classe | Prioridade interna | Comportamento |
|--------|-------------------|---------------|
| FIFO em tempo real | 0–99 (0 = mais alta) | Executa até bloquear ou ser preemptado por FIFO de prioridade superior |
| Circular em tempo real | 0–99 | Como FIFO mas com quantum; ao esgotar, vai para fim da lista |
| Tempo compartilhado | 100–139 | Algoritmo CFS; não compete com tempo real |

> [!note]
> "Tempo real" no Linux (POSIX P1003.4) não garante limites de tempo — apenas prioridade maior que threads convencionais.

## Nice

Cada thread tem valor **nice**: −20 (mais favorecido) a +19 (menos favorecido), padrão 0.

```c
nice(value);        // ajusta nice do thread atual
setpriority(...);   // versão mais geral
```

## CFS — Completely Fair Scheduler

Algoritmo para threads de tempo compartilhado (versões 2.6.23+).

### Mecanismo

Cada thread mantém `vruntime` (tempo virtual acumulado na CPU). CFS usa **árvore rubro-negra** ordenada por `vruntime`:
- Nodo mais à esquerda = menor `vruntime` = próximo a executar
- Thread com menor `vruntime` sempre recebe a CPU

### Equidade por Prioridade

CFS ajusta a **taxa de crescimento** do `vruntime` por prioridade:
- Prioridade mais alta → `vruntime` cresce mais devagar → permanece mais na CPU
- Prioridade mais baixa → `vruntime` cresce mais rápido → perde a CPU mais cedo

### Preempção

Ao acordar thread com menor `vruntime` que o thread atual:
- Se diferença excede `min_granularity`: preempção imediata
- Caso contrário: thread atual continua

## Temporização

- Versões antigas: relógio a 1.000 Hz, cada tick = 1 ms (jiffy)
- Versões novas: configurável para 500, 250 ou 1 Hz
- Modo "sem ciclo" (*tickless*): sem interrupção de timer quando sistema ocioso
- Temporizadores de alta resolução: controle em granularidade sub-jiffy

## Pontos Técnicos

- Tarefas interativas exigem baixa latência.
- Tarefas CPU-bound exigem divisão justa de processador.
- Migração entre CPUs pode aumentar vazão, mas pode degradar localidade de cache.
- Tempo real exige classes de escalonamento mais previsíveis.

## Sincronização no Núcleo

- `atomic_set`, `atomic_read`: operações atômicas sobre hardware
- `rmb`, `wmb`: barreiras de memória (evitam reordenamento pelo hardware)
- Travas giratórias (*spinlocks*): para threads que não querem bloquear
- Semáforos, mutexes: para seções críticas longas

## Ver também

- [[Escalonamento de CPU]]
- [[Processos no Linux]]
- [[Multiprocessadores]]
- [[Coerência de Cache]]
- [[Exclusão Mútua]]
