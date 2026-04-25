---
title: Monitor de Concorrência
aliases:
  - monitor
  - monitor de sincronização
  - synchronization monitor
tags:
  - computação/sistemas
source: "Sistemas Operacionais Modernos, cap. 2"
created_by: Codex
---

# Monitor de Concorrência

Nota criada pelo Codex.

## Definição

Abstração de sincronização que agrupa estado compartilhado, operações sobre esse estado e controle de entrada em uma mesma unidade.

## Papel técnico

O monitor deixa a exclusão mútua associada à estrutura que contém o recurso compartilhado. Em vez de espalhar travas pelo código, o acesso ocorre por procedimentos controlados pelo monitor.

## Relações (SPO)

- Monitor de Concorrência → encapsula → estado compartilhado
- Monitor de Concorrência → fornece → [[Exclusão Mútua]]
- Monitor de Concorrência → coordena → [[Thread|threads]]
- Monitor de Concorrência → reduz → erro manual de uso de [[Mutex]]
- Monitor de Concorrência → pertence a → sincronização de processos

