---
title: Kernel
aliases:
  - núcleo do sistema operacional
  - núcleo do SO
tags:
  - computação/sistemas
source: "Operating Systems: From 0 to 1, cap. 7-9"
created_by: Codex
---

# Kernel

Nota criada pelo Codex.

## Definição

**Kernel** é o núcleo privilegiado do sistema operacional. Ele gerencia CPU, memória e dispositivos, além de fornecer interfaces controladas para programas em espaço de usuário.

## Papel após o boot

Quando o bootloader transfere controle ao kernel, a responsabilidade por controlar hardware deixa de depender da BIOS. O kernel passa a usar código próprio, drivers e interfaces internas para operar recursos da máquina.

## Relações (SPO)

- [[Bootloader]] → carrega → Kernel
- Kernel → gerencia → CPU, memória e dispositivos
- Kernel → executa em → [[Espaço de Usuário e Espaço de Kernel]]
- Kernel → expõe serviços via → [[Chamada do Sistema]]
- Kernel → usa → [[Driver]]

