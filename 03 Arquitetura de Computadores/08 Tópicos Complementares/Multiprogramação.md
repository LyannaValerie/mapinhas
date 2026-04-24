---
title: Multiprogramação
aliases:
  - multiprogramming
  - vários programas em memória
tags:
  - computação/arquitetura
  - computação/sistemas-operacionais
date: 2026-04-18
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Multiprogramação

## Definição
Técnica em que **vários programas residem na memória principal simultaneamente**. Enquanto um programa aguarda conclusão de operação de E/S, o [[Processador]] executa outro — aumentando a utilização da CPU e o throughput do sistema.

Introduzida comercialmente com o [[História da Computação#IBM System/360 (1964)|IBM System/360]] (1964).

## Relações (SPO)
- Multiprogramação → mantém → múltiplos programas na memória simultaneamente
- Multiprogramação → aproveita → tempo ocioso de espera de E/S
- Multiprogramação → gerenciada por → [[Sistema Operacional]]
- Multiprogramação → requer → [[Proteção de Memória x86|proteção de memória]] entre programas
- Multiprogramação → precede → [[Multitarefa]] (extensão com preempção)

## Motivação

> [!tip] Por que surgiu
> Operações de E/S são ordens de magnitude mais lentas que o processador. Sem multiprogramação, a CPU fica ociosa esperando disco ou rede. Com ela, o tempo de espera de um programa vira tempo útil de outro.

```
Sem multiprogramação:
  [Programa A: executa] ──── [A: espera E/S] ──── [A: executa]
  CPU: ████████████████████░░░░░░░░░░░░░░░░░░░████

Com multiprogramação:
  [Programa A: executa] ──── [A: espera E/S] ──────────────────
  [Programa B: ─────── espera] ─── [B: executa durante E/S de A]
  CPU: ████████████████████████████████████████████
```

## Diferença em relação a Multitarefa

| Conceito | Troca de programa | Preempção |
|---|---|---|
| **Multiprogramação** | Apenas quando o programa bloqueia (E/S) | Não — voluntária |
| **[[Multitarefa]]** | Também por timer (quantum de tempo) | Sim — forçada pelo SO |

A multitarefa é uma extensão da multiprogramação com troca de contexto preemptiva.

## Ver também
- [[Sistema Operacional]]
- [[Processo]]
- [[Multitarefa]]
- [[Thread]]
- [[História da Computação]]
- [[Memória Virtual]]
