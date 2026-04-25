---
title: Variáveis BIOS
aliases:
  - BIOS data area
  - área de dados BIOS
  - BDA
tags:
  - computação/arquitetura
date: 2026-04-24
source: "PC System Programming — Abacus, cap. 7"
codex_note: criado pelo Codex
---

# Variáveis BIOS

## Definição

Variáveis BIOS são campos mantidos em memória baixa pelo [[BIOS do IBM PC]] para registrar estado de hardware, configuração e dados transitórios usados por suas rotinas.

## Conteúdo típico

- Endereços de portas seriais e paralelas.
- Estado de teclado e buffer de teclas.
- Parâmetros de vídeo e cursor.
- Contador de tempo.
- Status de disco e impressora.
- Flags de equipamento.

## Papel

A área de dados BIOS funciona como contrato compartilhado entre firmware e software em modo real. Programas podem consultar esses campos, mas escrita direta cria acoplamento forte com layout específico do PC.

## Relações (SPO)

- [[BIOS do IBM PC]] mantém [[Variáveis BIOS]].
- [[Serviços de BIOS do IBM PC]] leem e atualizam [[Variáveis BIOS]].
- [[BIOS Configuração do Sistema]] depende de campos de equipamento e memória.

## Ver também

- [[BIOS do IBM PC]]
- [[BIOS Configuração do Sistema]]
- [[BIOS Data e Hora]]
- [[Memória]]
