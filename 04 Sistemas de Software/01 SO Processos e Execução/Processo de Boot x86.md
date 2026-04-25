---
title: Processo de Boot x86
aliases:
  - x86 boot process
  - boot x86
tags:
  - computação/sistemas
  - computação/arquitetura
source: "Operating Systems: From 0 to 1, cap. 7"
created_by: Codex
---

# Processo de Boot x86

Nota criada pelo Codex.

## Definição

O **processo de boot x86** é a sequência de controle que leva a CPU do firmware inicial até o código do sistema operacional. Após o POST, a CPU começa executando código de [[BIOS]] em `FFFF:0000h`; a BIOS procura um setor inicial bootável e transfere controle para `0000:7C00h`.

## Sequência mínima

1. Firmware inicializa hardware básico.
2. BIOS verifica dispositivos de armazenamento.
3. BIOS identifica assinatura bootável `0x55 0xAA` no fim do primeiro setor.
4. BIOS carrega o setor em `0x7C00`.
5. CPU executa o [[Bootloader]].
6. Bootloader prepara ambiente e carrega o [[Kernel]].

## Relações (SPO)

- Processo de Boot x86 → começa em → [[BIOS]]
- BIOS → carrega → [[MBR]]
- MBR → contém → [[Bootloader]]
- Bootloader → transfere controle para → [[Kernel]]

