---
title: Depuração Bare Metal com QEMU e GDB
aliases:
  - debug bare metal
  - QEMU GDB bare metal
tags:
  - computação/sistemas
  - computação/ferramentas
source: "Operating Systems: From 0 to 1, cap. 7-8"
created_by: Codex
---

# Depuração Bare Metal com QEMU e GDB

Nota criada pelo Codex.

## Definição

**Depuração bare metal com QEMU e GDB** usa uma máquina emulada como alvo controlável de execução. QEMU fornece servidor GDB embutido; GDB conecta nesse servidor para inspecionar registradores, memória e fluxo de instruções.

## Detalhe operacional

Binários planos preservam pouco contexto simbólico. Para depuração mais rica, o bootloader pode ser montado como ELF com informações DWARF e depois convertido para binário plano com `objcopy` quando a imagem final exigir esse formato.

## Relações (SPO)

- QEMU → emula → máquina alvo
- QEMU → expõe → servidor GDB
- GDB → inspeciona → registradores e memória
- ELF com DWARF → melhora → depuração simbólica
- `objcopy` → converte → ELF em binário plano

