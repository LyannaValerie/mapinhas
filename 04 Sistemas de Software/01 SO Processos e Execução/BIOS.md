---
title: BIOS
aliases:
  - Basic Input Output System
  - firmware BIOS
tags:
  - computação/sistemas
  - computação/hardware
source: "Operating Systems: From 0 to 1, cap. 7"
created_by: Codex
---

# BIOS

Nota criada pelo Codex.

## Definição

**BIOS** (*Basic Input/Output System*) é firmware responsável por inicialização básica de hardware e por serviços genéricos de entrada e saída durante o boot legado x86.

## Papel no boot

Após o POST, a CPU executa código de BIOS. A BIOS procura um dispositivo bootável, verifica a assinatura `0x55 0xAA` no primeiro setor e carrega esse setor no endereço `0x7C00`.

## Relações (SPO)

- BIOS → inicializa → hardware básico
- BIOS → carrega → [[MBR]]
- BIOS → transfere controle para → [[Bootloader]]
- BIOS → opera em → [[Modo Real x86]]

