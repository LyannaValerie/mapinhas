---
title: Alocação de Memória DOS
aliases:
  - gerenciamento de memória DOS
  - memória DOS
  - MCB
tags:
  - computação/sistemas
date: 2026-04-24
created_by: Codex
source: desconhecida
---

# Alocação de Memória DOS

Nota criada pelo Codex.

## Definição

Alocação de memória DOS é o mecanismo pelo qual [[MS-DOS]] reserva, redimensiona e libera blocos de memória para programas em modo real.

## Relações (SPO)
- DOS → aloca → blocos de memória
- Bloco de memória → medido em → parágrafos
- Parágrafo → equivale a → 16 bytes
- [[Programa COM]] → tende a receber → toda memória livre
- [[EXEC DOS]] → exige → memória livre para programa filho
- [[Programa EXE]] → declara → memória mínima e máxima

---

## Implicação

Em DOS, o carregamento do programa e a gestão de memória são acoplados. Um programa que pretende executar outro programa precisa manter espaço disponível. Caso contrário, a função [[EXEC DOS]] não consegue alocar o bloco necessário para o filho.

## Ver também
- [[MS-DOS]]
- [[Programa COM]]
- [[Programa EXE]]
- [[EXEC DOS]]
- [[Segmentação]]

