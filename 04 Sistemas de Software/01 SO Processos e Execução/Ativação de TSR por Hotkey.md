---
title: Ativação de TSR por Hotkey
aliases:
  - hotkey TSR
  - tecla de ativação TSR
  - ativação de programa residente
tags:
  - computação/sistemas
date: 2026-04-24
created_by: Codex
source: "PC System Programming, cap. 8"
---

# Ativação de TSR por Hotkey

Nota criada pelo Codex.

## Definição

Ativação de TSR por hotkey é o mecanismo em que um [[Programa TSR DOS]] fica residente e passa ao primeiro plano quando uma combinação de teclas é detectada.

## Relações (SPO)
- Hotkey → ativa → [[Programa TSR DOS]]
- TSR → intercepta → interrupção de teclado
- `INT 09h` → ocorre em → pressionamento ou liberação de tecla
- `INT 16h` → fornece → serviço BIOS de teclado
- Handler TSR → deve chamar → handler anterior
- Ativação por hotkey → inicia → [[Troca de Contexto TSR]]

---

## Interrupções Relevantes

`INT 16h` é serviço BIOS de teclado usado por programas para ler caracteres e estado do teclado. Se o TSR depender apenas de `INT 16h`, ele só percebe a hotkey quando o programa em primeiro plano usa esse serviço.

`INT 09h` é mais adequado para ativação assíncrona porque ocorre quando uma tecla é pressionada ou liberada. O TSR pode redirecionar esse vetor para seu próprio handler, verificar a combinação de teclas e depois encadear para a rotina anterior.

## Ver também
- [[Programa TSR DOS]]
- [[Interrupção]]
- [[Tabela de Vetores de Interrupção]]
- [[BIOS do IBM PC]]
- [[Troca de Contexto TSR]]
