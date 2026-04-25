---
title: Tratador de Interrupção
aliases:
  - handler de interrupção
  - interrupt handler
  - rotina de tratamento de interrupção
tags:
  - computação/sistemas
source: "Sistemas Operacionais Modernos, cap. 5"
created_by: Codex
---

# Tratador de Interrupção

Conteúdo criado pelo Codex.

## Definição

**Tratador de interrupção** é a rotina do kernel executada após uma [[Interrupção]] para reconhecer o evento, salvar ou restaurar contexto e liberar o fluxo bloqueado pela E/S.

## Papel técnico

O tratador deve executar trabalho mínimo e previsível. Operações longas tendem a ser adiadas para camadas superiores, para reduzir latência de interrupção e manter o sistema responsivo.

## Relações (SPO)

- Tratador de Interrupção -> atende -> [[Interrupção]]
- Tratador de Interrupção -> notifica -> [[Driver]]
- Tratador de Interrupção -> pode desbloquear -> [[Processo]]
- Tratador de Interrupção -> depende de -> estado salvo da CPU

