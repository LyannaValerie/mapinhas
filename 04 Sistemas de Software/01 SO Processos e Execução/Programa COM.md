---
title: Programa COM
aliases:
  - arquivo COM
  - formato COM
tags:
  - computação/sistemas
date: 2026-04-24
created_by: Codex
source: desconhecida
---

# Programa COM

Nota criada pelo Codex.

## Definição

Programa COM é um formato executável simples do [[MS-DOS]]. Ele não possui cabeçalho de relocação elaborado: o programa é carregado em um único segmento, após o [[Program Segment Prefix]].

## Relações (SPO)
- Programa COM → usa → um segmento
- Código, dados e pilha → compartilham → mesmo segmento
- DOS → carrega → COM após PSP
- COM → tem entrada convencional → deslocamento `0100h`
- COM → contrasta com → [[Programa EXE]]

---

## Consequência Operacional

Como DOS tende a reservar a memória livre para programas COM, um programa COM que precisa chamar outro programa via [[EXEC DOS]] deve liberar memória excedente antes da chamada. Sem isso, o programa filho pode não ter espaço para carregar.

## Ver também
- [[MS-DOS]]
- [[Program Segment Prefix]]
- [[Programa EXE]]
- [[Alocação de Memória DOS]]

