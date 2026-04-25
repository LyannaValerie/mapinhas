---
title: Troca de Contexto TSR
aliases:
  - context switch TSR
  - troca de contexto em TSR
  - contexto de programa residente
tags:
  - computação/sistemas
date: 2026-04-24
created_by: Codex
source: "PC System Programming, cap. 8"
---

# Troca de Contexto TSR

Nota criada pelo Codex.

## Definição

Troca de contexto TSR é o procedimento que permite a um [[Programa TSR DOS]] interromper um programa ativo, executar sua própria rotina e restaurar o estado necessário para que o programa interrompido continue corretamente.

## Relações (SPO)
- Troca de contexto TSR → salva → registradores
- Troca de contexto TSR → preserva → pilha
- Troca de contexto TSR → preserva → PSP
- Troca de contexto TSR → preserva → DTA
- [[Program Segment Prefix]] → identifica → contexto DOS do programa
- DTA → armazena → área de transferência de operações de arquivo
- Troca de contexto TSR → exige → transparência de interrupção

---

## Estado Preservado

O contexto inclui estado de CPU e estado dependente do DOS. Além de registradores e pilha, o TSR precisa considerar estruturas como [[Program Segment Prefix|PSP]] e DTA. Essas estruturas conectam chamadas DOS ao programa corrente; se forem deixadas apontando para o processo errado, operações de arquivo, terminação ou acesso a parâmetros podem corromper o estado do programa interrompido.

## Implicação

TSR é mais sensível que uma chamada de sub-rotina comum porque entra por interrupção. O código residente precisa salvar estado antes de executar e restaurar estado antes de retornar ao fluxo interrompido.

## Ver também
- [[Programa TSR DOS]]
- [[Program Segment Prefix]]
- [[Interrupção]]
- [[Pilha]]
- [[Segurança de Reentrada DOS]]
