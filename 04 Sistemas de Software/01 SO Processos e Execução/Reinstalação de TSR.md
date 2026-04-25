---
title: Reinstalação de TSR
aliases:
  - desinstalação de TSR
  - remoção de TSR
  - cadeia de TSR
  - LIFO TSR
tags:
  - computação/sistemas
date: 2026-04-24
created_by: Codex
source: "PC System Programming, cap. 8"
---

# Reinstalação de TSR

Nota criada pelo Codex.

## Definição

Reinstalação de TSR é a tentativa de remover ou restaurar um [[Programa TSR DOS]] residente, recolocando vetores de interrupção e liberando sua posição lógica na cadeia de handlers.

## Relações (SPO)
- TSR → altera → [[Tabela de Vetores de Interrupção]]
- TSR → salva → vetores antigos
- Handler TSR → encadeia para → handler anterior
- Reinstalação de TSR → segue → ordem LIFO
- Ordem LIFO → exige → remover primeiro o TSR instalado por último
- Vetor de interrupção → deve apontar para → handler do TSR removido

---

## Restrição LIFO

Quando vários TSRs interceptam a mesma interrupção, cada instalação empilha um novo handler sobre o anterior. A remoção segura só é trivial quando o TSR a remover ainda é o último da cadeia.

Se outro TSR foi instalado depois, o vetor aponta para o handler mais recente, não para o TSR antigo. Remover o TSR intermediário sem reencadear corretamente pode quebrar a cadeia de interrupções.

## Ver também
- [[Programa TSR DOS]]
- [[Tabela de Vetores de Interrupção]]
- [[Interrupção]]
- [[Ativação de TSR por Hotkey]]
