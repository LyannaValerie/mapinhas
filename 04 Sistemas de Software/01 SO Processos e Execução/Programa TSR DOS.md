---
title: Programa TSR DOS
aliases:
  - TSR
  - Terminate and Stay Resident
  - programa residente DOS
  - programa residente em memória
tags:
  - computação/sistemas
date: 2026-04-24
created_by: Codex
source: "PC System Programming, cap. 8"
---

# Programa TSR DOS

Nota criada pelo Codex.

## Definição

Programa TSR DOS é um programa de [[MS-DOS]] que termina sua fase de inicialização, mas mantém parte de seu código e dados residente em memória para ser reativado depois por algum evento externo.

Esse modelo não implementa [[Multitarefa]] real: em cada instante só há um fluxo principal usando o processador. O TSR simula presença em segundo plano porque intercepta eventos, salva contexto, executa sua rotina e devolve controle ao programa interrompido.

## Relações (SPO)
- Programa TSR DOS → permanece → residente em memória
- Programa TSR DOS → intercepta → [[Interrupção]]
- Programa TSR DOS → usa → [[Tabela de Vetores de Interrupção]]
- Programa TSR DOS → depende de → [[Troca de Contexto TSR]]
- Programa TSR DOS → deve respeitar → [[Segurança de Reentrada DOS]]
- Programa TSR DOS → pode ser ativado por → [[Ativação de TSR por Hotkey]]
- Programa TSR DOS → usa estado DOS de → [[Program Segment Prefix]]

---

## Estrutura Operacional

Um TSR tem duas fases:

1. instalação: configura vetores de interrupção, salva endereços antigos e deixa a parte residente na memória;
2. ativação: uma interrupção chama o handler do TSR, que decide se deve executar ou apenas encadear para o handler anterior.

O ponto crítico é transparência. O programa interrompido deve continuar depois como se a interrupção nunca tivesse ocorrido.

## Ver também
- [[MS-DOS]]
- [[Interrupção]]
- [[Tabela de Vetores de Interrupção]]
- [[Ativação de TSR por Hotkey]]
- [[Troca de Contexto TSR]]
- [[Segurança de Reentrada DOS]]
- [[Reinstalação de TSR]]
