---
title: MBR
aliases:
  - Master Boot Record
  - setor mestre de inicialização
tags:
  - computação/sistemas
  - computação/armazenamento
source: "Operating Systems: From 0 to 1, cap. 7"
created_by: Codex
---

# MBR

Nota criada pelo Codex.

## Definição

**MBR** (*Master Boot Record*) é o primeiro setor de um dispositivo de armazenamento no modelo clássico de boot por BIOS. Para ser considerado bootável, termina com a assinatura `0x55 0xAA` nos dois últimos bytes do setor de 512 bytes.

## Papel técnico

O MBR contém o primeiro código executável do bootloader. Como o espaço é pequeno, esse código normalmente só inicializa o mínimo necessário e carrega código adicional de outros setores.

## Relações (SPO)

- MBR → é lido por → [[BIOS]]
- MBR → contém → [[Bootloader]]
- MBR → termina com → assinatura `0x55 0xAA`
- BIOS → carrega MBR em → endereço `0x7C00`

