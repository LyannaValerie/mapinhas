---
title: Spooling
aliases:
  - spool
  - Simultaneous Peripheral Operation On-Line
tags:
  - computação/sistemas
source: "Sistemas Operacionais Modernos, cap. 5"
created_by: Codex
---

# Spooling

Conteúdo criado pelo Codex.

## Definição

**Spooling** é a técnica de colocar requisições de E/S em uma fila persistente ou intermediária para um dispositivo dedicado ser usado de modo serializado.

## Exemplo típico

Impressão usa spool porque a impressora atende um trabalho por vez. Processos enviam arquivos para uma fila, e um serviço separado alimenta o dispositivo.

## Relações (SPO)

- Spooling -> serializa -> acesso a dispositivo dedicado
- Spooling -> usa -> armazenamento intermediário
- Spooling -> pode executar em -> [[Software de E-S do Espaço do Usuário|Software de E/S do Espaço do Usuário]]
- Spooling -> reduz -> bloqueio direto do processo no dispositivo
