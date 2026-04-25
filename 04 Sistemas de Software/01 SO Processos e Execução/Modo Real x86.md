---
title: Modo Real x86
aliases:
  - real mode
  - modo real
tags:
  - computação/arquitetura
  - computação/sistemas
source: "Operating Systems: From 0 to 1, cap. 7"
created_by: Codex
---

# Modo Real x86

Nota criada pelo Codex.

## Definição

**Modo Real x86** é o modo inicial de execução compatível com o ambiente legado da BIOS. Nele, o bootloader pode chamar interrupções de BIOS, como serviços de disco, vídeo e teclado.

## Implicação para sistemas operacionais

Serviços de BIOS deixam de ser a base operacional quando o sistema muda para modos protegidos. Depois dessa transição, o kernel precisa controlar hardware por código próprio, normalmente via [[Driver]] ou camadas equivalentes.

## Relações (SPO)

- CPU x86 → inicia boot legado em → Modo Real x86
- [[Bootloader]] → usa → interrupções de BIOS
- [[BIOS]] → é utilizável em → Modo Real x86
- [[Kernel]] → substitui dependência de BIOS por → drivers

