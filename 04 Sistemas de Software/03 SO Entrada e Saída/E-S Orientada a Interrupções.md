---
title: E/S Orientada a Interrupções
aliases:
  - entrada e saída orientada a interrupções
  - interrupt-driven I/O
tags:
  - computação/sistemas
source: "Sistemas Operacionais Modernos, cap. 5"
created_by: Codex
---

# E/S Orientada a Interrupções

Conteúdo criado pelo Codex.

## Definição

**E/S orientada a interrupções** é o método em que o dispositivo interrompe a CPU quando precisa de serviço ou conclui uma etapa da operação.

## Papel técnico

A CPU não precisa sondar continuamente o dispositivo. O processo solicitante pode ser bloqueado, outro processo pode executar, e o [[Tratador de Interrupção]] retoma o fluxo quando o hardware sinaliza progresso.

## Relações (SPO)

- E/S Orientada a Interrupções -> evita -> espera ocupada contínua
- E/S Orientada a Interrupções -> usa -> [[Tratador de Interrupção]]
- [[Controlador de Dispositivo]] -> gera -> [[Interrupção]]
- E/S Orientada a Interrupções -> pode ter -> overhead por interrupção
- E/S Orientada a Interrupções -> contrasta com -> [[E-S Programada|E/S Programada]]
