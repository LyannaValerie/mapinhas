---
title: Serviços de BIOS do IBM PC
aliases:
  - chamadas BIOS
  - BIOS interrupts
  - rotinas BIOS do PC
tags:
  - computação/arquitetura
date: 2026-04-24
source: "PC System Programming — Abacus, cap. 7"
codex_note: criado pelo Codex
---

# Serviços de BIOS do IBM PC

## Definição

Serviços de BIOS do IBM PC são rotinas de firmware chamadas por [[Interrupção|interrupções]] para acessar funções básicas de vídeo, disco, teclado, impressora, porta serial, relógio e configuração do sistema.

## Chamadas principais

- `INT 10h`: serviços de vídeo. Ver [[BIOS INT 10h]].
- `INT 13h`: serviços de disco. Ver [[BIOS INT 13h]].
- `INT 16h`: serviços de teclado. Ver [[BIOS INT 16h]].
- `INT 17h`: serviços de impressora. Ver [[BIOS INT 17h]].
- `INT 14h`: serviços de porta serial. Ver [[BIOS Porta Serial]].
- `INT 1Ah`: data, hora e relógio. Ver [[BIOS Data e Hora]].

## Funcionamento

O programa seleciona função por registradores, executa a interrupção e recebe status ou dados também por registradores. Esse contrato reduz dependência direta de controladores específicos.

## Relações (SPO)

- [[BIOS do IBM PC]] oferece [[Serviços de BIOS do IBM PC]].
- [[Serviços de BIOS do IBM PC]] usam [[Tabela de Vetores de Interrupção]].
- [[MS-DOS]] pode chamar serviços BIOS para operações de baixo nível.

## Ver também

- [[BIOS do IBM PC]]
- [[Interrupção]]
- [[Tabela de Vetores de Interrupção]]
- [[BIOS Configuração do Sistema]]
