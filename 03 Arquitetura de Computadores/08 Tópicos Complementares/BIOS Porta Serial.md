---
title: BIOS Porta Serial
aliases:
  - BIOS INT 14h
  - interrupção serial BIOS
  - serviços seriais BIOS
tags:
  - computação/arquitetura
date: 2026-04-24
source: "PC System Programming — Abacus, cap. 7"
codex_note: criado pelo Codex
---

# BIOS Porta Serial

## Definição

`INT 14h` é o serviço de [[BIOS do IBM PC]] para inicialização, envio, recepção e consulta de status de porta serial.

## Funções típicas

- Inicializa parâmetros de comunicação.
- Envia caractere.
- Recebe caractere.
- Lê status da linha e do modem.

## Parâmetros

Configuração serial inclui taxa de transmissão, paridade, bits de dados e bits de parada. O BIOS oferece interface mínima; protocolos e tratamento avançado ficam no software.

## Relações (SPO)

- [[BIOS Porta Serial]] pertence a [[Serviços de BIOS do IBM PC]].
- [[BIOS Porta Serial]] abstrai hardware de comunicação assíncrona.
- [[Portas de Comunicação]] expõem conexões físicas usadas por periféricos seriais.

## Ver também

- [[BIOS do IBM PC]]
- [[Portas de Comunicação]]
- [[Serviços de BIOS do IBM PC]]
