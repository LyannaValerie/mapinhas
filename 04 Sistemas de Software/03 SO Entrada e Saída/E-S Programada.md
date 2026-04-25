---
title: E/S Programada
aliases:
  - entrada e saída programada
  - programmed I/O
  - PIO
tags:
  - computação/sistemas
source: "Sistemas Operacionais Modernos, cap. 5"
created_by: Codex
---

# E/S Programada

Conteúdo criado pelo Codex.

## Definição

**E/S programada** é o método em que a CPU executa diretamente a transferência de dados entre memória e dispositivo.

## Custo

O método é simples, mas desperdiça CPU quando exige espera ocupada. Ele pode fazer sentido em sistemas pequenos, dispositivos lentos ou trechos onde não há outro trabalho útil para executar.

## Relações (SPO)

- E/S Programada -> usa -> CPU para transferência
- E/S Programada -> pode causar -> espera ocupada
- E/S Programada -> contrasta com -> [[E-S Orientada a Interrupções|E/S Orientada a Interrupções]]
- E/S Programada -> contrasta com -> [[DMA]]
