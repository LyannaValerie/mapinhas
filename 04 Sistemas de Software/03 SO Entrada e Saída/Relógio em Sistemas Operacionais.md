---
title: Relógio em Sistemas Operacionais
aliases:
  - relógio do sistema operacional
  - clock em sistemas operacionais
  - temporizador de hardware
tags:
  - computação/sistemas
source: "Sistemas Operacionais Modernos, cap. 5"
created_by: Codex
---

# Relógio em Sistemas Operacionais

Conteúdo criado pelo Codex.

## Definição

**Relógio em sistemas operacionais** é o mecanismo de hardware e software usado para medir tempo, gerar interrupções periódicas e limitar a execução de processos.

## Funções

- manter hora do dia
- preemptar processos após fatia de tempo
- contabilizar uso de CPU
- implementar alarmes
- acionar temporizadores watchdog
- apoiar perfilamento e monitoramento

## Relações (SPO)

- Relógio em Sistemas Operacionais -> gera -> [[Interrupção]]
- Relógio em Sistemas Operacionais -> apoia -> [[Escalonamento de CPU]]
- Relógio em Sistemas Operacionais -> implementa -> fatia de tempo
- Relógio em Sistemas Operacionais -> fornece base para -> [[Temporizador por Software]]

