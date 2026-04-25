---
title: Bootloader
aliases:
  - carregador de boot
  - boot loader
tags:
  - computação/sistemas
source: "Operating Systems: From 0 to 1, cap. 7"
created_by: Codex
---

# Bootloader

Nota criada pelo Codex.

## Definição

**Bootloader** é o programa inicial carregado pelo firmware para preparar a máquina e transferir controle ao [[Kernel]]. Em máquinas x86 com BIOS, o primeiro estágio costuma residir no [[MBR]] e começa a executar no endereço físico `0x7C00`.

## Função no boot

- inicializa registradores de segmento e pilha
- usa serviços de [[BIOS]] enquanto a CPU ainda está em [[Modo Real x86]]
- carrega setores adicionais do disco para memória
- prepara transição para modo de execução mais capaz
- salta para o código do kernel ou de um segundo estágio

## Relações (SPO)

- Bootloader → é carregado por → [[BIOS]]
- Bootloader → reside inicialmente em → [[MBR]]
- Bootloader → carrega → [[Kernel]]
- Bootloader → usa → serviços de BIOS em [[Modo Real x86]]
- Bootloader → pode ser depurado com → [[Depuração Bare Metal com QEMU e GDB]]

