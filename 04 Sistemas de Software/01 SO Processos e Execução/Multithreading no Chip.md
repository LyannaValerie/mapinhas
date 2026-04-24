---
title: Multithreading no Chip
aliases:
  - chip multithreading
  - multithreading de granulação fina
  - multithreading de granulação grossa
  - simultaneous multithreading
  - SMT
  - hyperthreading
  - HT
  - fine-grained multithreading
  - coarse-grained multithreading
tags:
  - computação/arquitetura
date: 2026-04-13
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Sistemas de Software]]

# Multithreading no Chip

## Problema

Toda CPU com pipeline sofre com **ausências de cache**: quando uma referência à memória não encontra dado em L1 nem L2, o pipeline para esperando a busca da memória principal. Solução: fazer a CPU gerenciar **múltiplos threads** — quando um thread bloqueia, a CPU executa outro.

## Relações (SPO)
- Multithreading no chip → mascara → latências de memória
- Multithreading de granulação fina → alterna → threads a cada ciclo de clock
- Multithreading de granulação grossa → troca → thread somente quando o atual protela
- SMT → combina → superescalar + multithreading para máxima utilização
- Hyperthreading → é → implementação Intel de SMT no Core i7
- Cada thread → requer → seu próprio conjunto de registradores

---

## Multithreading de Granulação Fina

Alterna entre threads em **ciclos consecutivos** (round-robin). Se o thread A protelar no ciclo N, o ciclo N+1 executa o thread B, e assim por diante.

```
Ciclo:  1    2    3    4    5    6    7    8    9   10   11   12
CPU:   [A1] [B1] [C1] [A2] [B2] [C2] [A3] [B3] [C3] [A4] [B4] [C4]
```

- Com k estágios de pipeline e pelo menos k threads, nunca há mais de uma instrução por thread no pipeline → sem conflitos de dependência
- **Desvantagem:** mesmo threads sem bloqueio são atrasados pela alternância

---

## Multithreading de Granulação Grossa

Thread atual executa até **protelar**; então ocorre troca. Limpa o pipeline na troca.

```
Ciclo:  1    2    3    4    5    6    7    8    9   ...
CPU:   [A1] [A2] [--] [B1] [--] [C1] [C2] [C3] [C4]...
```

- Perde 1 ciclo por troca (esvaziar pipeline)
- Melhor quando threads ativos são poucos (não há threads suficientes para granulação fina)
- Alternativa: trocar antecipadamente em instruções suspeitas (load, store, branch) — mescla com granulação fina

---

## Multithreading Simultâneo (SMT)

Refinamento para CPUs **superescalares** (emissão múltipla). Um thread emite múltiplas instruções por ciclo; quando protela, instruções de outro thread preenchem os slots ociosos.

```
Ciclo superescalar de emissão dual:
Slot 1: [A1] [B1] [C2] [C4] [A6] [A8]
Slot 2: [A2] [C1] [C3] [A4] [A7] [C5]
```

- Maximiza utilização de todas as unidades funcionais
- Quando uma unidade funcional está ocupada para o thread atual, usa instrução de outro thread

---

## Hyperthreading no Core i7

Implementação Intel de SMT. Cada núcleo parece **dois processadores lógicos** para o SO.

### Estratégias de compartilhamento de recursos

| Estratégia | Recurso | Motivo |
|---|---|---|
| **Duplicação** | Contador de programa, mapa de registradores, controlador de interrupção | Cada thread precisa sempre; overhead de área ~5% |
| **Particionamento** | Filas do pipeline (metade para cada thread) | Evita que um thread afogue o outro |
| **Compartilhamento dinâmico c/ limite** | Escalonador | Flexibilidade sem risco de starvation |
| **Compartilhamento total** | Linhas de cache, unidades de execução | Abundantes; sem risco de monopolização |

### Ganho vs. custo
- +5% de área do chip → +25% de desempenho em muitas aplicações
- **Risco:** dois threads que cada um precisa de 3/4 da cache → muitas ausências de cache quando executam juntos

---

## Número de threads necessários

| Tipo de MT | Threads para latência L cycles |
|---|---|
| Granulação fina | ≥ L threads (1 por estágio do pipeline) |
| Granulação grossa | Muito menos (troca em protelar) |
| SMT | 2–4 threads por núcleo na prática |

---

## Ver também
- [[Pipeline]] — base do problema de latência
- [[Hierarquia de Memória]] — ausência de cache como causa do bloqueio
- [[Concorrência e Paralelismo]] — visão geral de paralelismo no chip
- [[Multiprocessadores]] — múltiplas CPUs vs. múltiplos threads por CPU
