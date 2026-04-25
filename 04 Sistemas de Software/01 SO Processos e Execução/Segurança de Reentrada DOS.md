---
title: Segurança de Reentrada DOS
aliases:
  - reentrada DOS
  - InDOS
  - flag InDOS
  - INT 28h
tags:
  - computação/sistemas
date: 2026-04-24
created_by: Codex
source: "PC System Programming, cap. 8"
---

# Segurança de Reentrada DOS

Nota criada pelo Codex.

## Definição

Segurança de reentrada DOS é a restrição que impede um [[Programa TSR DOS]] de chamar serviços do [[MS-DOS]] enquanto o próprio DOS já está executando uma chamada não segura para reentrada.

## Relações (SPO)
- DOS → mantém → flag InDOS
- Flag InDOS → indica → execução ativa de função DOS
- TSR → consulta → flag InDOS
- `INT 28h` → sinaliza → janela ociosa do DOS
- Chamada DOS reentrante → pode causar → corrupção de estado
- Segurança de reentrada DOS → limita → [[Ativação de TSR por Hotkey]]

---

## Regra Operacional

Se a flag InDOS indica que DOS está ocupado, o TSR não deve chamar funções DOS arbitrárias. A ativação pode ser adiada até um ponto mais seguro.

`INT 28h` é relevante porque ocorre enquanto DOS espera entrada ou está em estado ocioso curto. Nesse ponto, certas ativações de TSR são menos arriscadas que durante uma chamada DOS comum.

## Ver também
- [[Programa TSR DOS]]
- [[MS-DOS]]
- [[Chamada do Sistema]]
- [[Interrupção]]
- [[Troca de Contexto TSR]]
