---
title: Inicialização do IBM PC
aliases:
  - boot do IBM PC
  - bootstrap loader do PC
  - POST do IBM PC
tags:
  - computação/arquitetura
date: 2026-04-24
source: "PC System Programming — Abacus, cap. 7"
codex_note: criado pelo Codex
---

# Inicialização do IBM PC

## Definição

Inicialização do IBM PC é a sequência executada pelo [[BIOS do IBM PC]] após reset para colocar hardware mínimo em estado utilizável e transferir controle para código de boot.

## Sequência

- Inicializa registradores, controladores e tabelas básicas usadas pelo firmware.
- Executa verificações de sistema e testes de periféricos.
- Procura dispositivo inicializável.
- Carrega o setor de boot para memória.
- Transfere execução para o bootstrap loader.

## Relações (SPO)

- [[BIOS do IBM PC]] executa [[Inicialização do IBM PC]].
- [[Inicialização do IBM PC]] antecede carregamento de [[MS-DOS]].
- [[Firmware]] encapsula testes iniciais de [[Hardware]].
- [[Tabela de Vetores de Interrupção]] viabiliza chamadas iniciais por interrupção.

## Falhas

Falhas de boot podem ocorrer por ausência de mídia inicializável, erro de leitura, configuração incorreta de dispositivo ou falha em periférico essencial.

## Ver também

- [[BIOS do IBM PC]]
- [[Firmware]]
- [[MS-DOS]]
- [[Tabela de Vetores de Interrupção]]
