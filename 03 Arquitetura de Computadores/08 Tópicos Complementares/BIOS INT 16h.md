---
title: BIOS INT 16h
aliases:
  - interrupção de teclado BIOS
  - BIOS keyboard interrupt
  - serviços de teclado BIOS
tags:
  - computação/arquitetura
date: 2026-04-24
source: "PC System Programming — Abacus, cap. 7"
codex_note: criado pelo Codex
---

# BIOS INT 16h

## Definição

`INT 16h` é o serviço de [[BIOS do IBM PC]] para acesso ao teclado, leitura de teclas e consulta de estado do buffer de teclado.

## Funções

| AH | Função | Saída |
|---|---|---|
| 00h | Ler caractere (bloqueante) | AH=scan code, AL=ASCII (espera se buffer vazio) |
| 01h | Verificar se tecla disponível (não remove) | ZF=0 → AH=scan, AL=ASCII; ZF=1 → buffer vazio |
| 02h | Ler byte de status do teclado | AL=byte de flags (ver tabela abaixo) |

## Byte de status (fn 02h → AL)

| Bit | Significado |
|---|---|
| 0 | Right Shift pressionado |
| 1 | Left Shift pressionado |
| 2 | Ctrl pressionado |
| 3 | Alt pressionado |
| 4 | ScrollLock ativo |
| 5 | NumLock ativo |
| 6 | CapsLock ativo |
| 7 | Insert ativo |

## Buffer de teclado

O BIOS mantém buffer circular de teclado (16 entradas de 2 bytes cada) em memória para desacoplar evento físico da leitura pelo programa:
- `fn 00h`: leitura **bloqueante** — suspende programa até tecla disponível
- `fn 01h`: teste **não-bloqueante** sem remover do buffer (usa ZF como indicador)

## Scan codes vs ASCII

Teclas especiais (F1–F12, setas, Insert, Delete) retornam `AL=00h` e scan code em `AH`. Teclas de caractere retornam ASCII em `AL`.

> [!note] Interação com INT 9h
> INT 9h é o handler de hardware do teclado (IRQ 1). Ele lê o scan code da porta `60h` e converte para par ASCII/scan antes de inserir no buffer. INT 16h apenas consome esse buffer.

## Relação com interrupção de hardware

O BIOS também expõe [[Interrupção]] `1Bh` (Ctrl+Break), que é disparada dentro do handler INT 9h quando `Ctrl+Break` é detectado.

## Relações (SPO)

- [[BIOS INT 16h]] pertence a [[Serviços de BIOS do IBM PC]].
- [[BIOS INT 16h]] abstrai [[Teclado]] para programas em modo real.
- [[BIOS INT 16h]] usa códigos de varredura produzidos pelo controlador de teclado.

## Ver também

- [[BIOS do IBM PC]]
- [[Teclado]]
- [[Interrupção]]
- [[Serviços de BIOS do IBM PC]]
