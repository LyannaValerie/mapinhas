---
title: Driver
aliases:
  - driver de dispositivo
  - device driver
tags:
  - computação/sistemas
source: "Operating Systems: From 0 to 1, cap. 9"
created_by: Codex
---

# Driver

Nota criada pelo Codex.

## Definição

**Driver** é o componente de software que implementa controle específico de um dispositivo de hardware para o sistema operacional. Ele encapsula detalhes do dispositivo atrás de interfaces usadas pelo kernel.

## Relação com HAL

A [[HAL]] define contratos estáveis entre kernel e drivers. O driver implementa esses contratos para um dispositivo ou classe de dispositivos.

## Relações (SPO)

- Driver → controla → dispositivo de hardware
- Driver → implementa → interface da [[HAL]]
- [[Kernel]] → usa → Driver
- Driver → reduz → acoplamento entre kernel e hardware específico

