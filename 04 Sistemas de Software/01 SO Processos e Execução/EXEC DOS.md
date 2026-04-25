---
title: EXEC DOS
aliases:
  - função EXEC
  - INT 21h 4Bh
  - DOS EXEC
tags:
  - computação/sistemas
date: 2026-04-24
created_by: Codex
source: desconhecida
---

# EXEC DOS

Nota criada pelo Codex.

## Definição

EXEC é a função do [[MS-DOS]] usada para carregar e iniciar outro programa. Ela é chamada por `INT 21h`, função `4Bh`.

## Relações (SPO)
- EXEC → carrega → programa filho
- EXEC → depende de → memória disponível
- Programa pai → deve liberar → memória excedente
- Programa filho → recebe → [[Program Segment Prefix]]
- Carregador → interpreta → [[Programa COM]] ou [[Programa EXE]]

---

## Restrição de Memória

EXEC falha se não houver memória suficiente para o programa filho. Em programas COM, o problema é comum porque DOS pode reservar toda a memória livre para o programa corrente. Antes de chamar EXEC, o programa pai precisa reduzir seu bloco de memória para deixar espaço ao filho.

## Ver também
- [[MS-DOS]]
- [[Alocação de Memória DOS]]
- [[Programa COM]]
- [[Programa EXE]]
- [[Processo]]

