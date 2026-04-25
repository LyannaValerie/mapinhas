---
title: Temporizador por Software
aliases:
  - software timer
  - temporizadores por software
tags:
  - computação/sistemas
source: "Sistemas Operacionais Modernos, cap. 5"
created_by: Codex
---

# Temporizador por Software

Conteúdo criado pelo Codex.

## Definição

**Temporizador por software** é uma abstração mantida pelo sistema operacional para disparar eventos futuros sem exigir um temporizador físico dedicado para cada evento.

## Papel técnico

O kernel mantém uma estrutura de timers pendentes e usa interrupções do [[Relógio em Sistemas Operacionais]] para atualizar contadores, expirar eventos e acordar processos.

## Relações (SPO)

- Temporizador por Software -> depende de -> [[Relógio em Sistemas Operacionais]]
- Temporizador por Software -> agenda -> evento futuro
- Temporizador por Software -> pode acordar -> [[Processo]]
- Temporizador por Software -> pode implementar -> timeout

