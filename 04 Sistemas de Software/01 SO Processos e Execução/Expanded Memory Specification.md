---
title: Expanded Memory Specification
aliases:
  - EMS
  - LIM EMS
  - Expanded Memory Manager
  - EMM
tags:
  - computação/sistemas
  - computação/memória
date: 2026-04-24
source: "PC System Programming — Abacus, cap. 13"
codex_note: criado pelo Codex
---

# Expanded Memory Specification

## Definição

Expanded Memory Specification é um padrão de memória para DOS que expõe memória adicional por uma janela dentro do espaço de endereçamento de 1 MiB do 8088. A memória expandida não é a mesma coisa que memória estendida: EMS é acessada por comutação de bancos, enquanto memória estendida fica acima de 1 MiB.

## Page frame

O padrão LIM usa uma janela de 64 KiB, normalmente no segmento `D000h`. Essa janela é dividida em quatro páginas físicas de 16 KiB. O hardware EMS mapeia páginas lógicas da placa para essas páginas físicas visíveis no espaço endereçável do processador.

## EMM

O Expanded Memory Manager fornece interface de software por `INT 67h`. Antes de usar EMS, o programa deve verificar se o EMM existe, porque chamada sem EMM instalado tem resultado imprevisível.

Fluxo típico:

1. verificar status do EMM;
2. consultar páginas livres;
3. alocar páginas EMS;
4. mapear página lógica para página física do page frame;
5. liberar páginas ao final.

Funções centrais:

- `01h`: status do EMM;
- `02h`: segmento do page frame;
- `03h`: número de páginas;
- `04h`: alocar páginas;
- `05h`: definir mapeamento;
- `06h`: liberar páginas.

## Relações (SPO)

- [[Expanded Memory Specification]] contorna limite convencional de memória do [[MS-DOS]].
- [[Expanded Memory Specification]] usa [[Driver de Dispositivo DOS]] para instalar o EMM.
- [[Expanded Memory Specification]] chama interface por [[Interrupção]] `67h`.
- [[Expanded Memory Specification]] complementa [[Alocação de Memória DOS]].

## Ver também

- [[MS-DOS]]
- [[Alocação de Memória DOS]]
- [[Driver de Dispositivo DOS]]
- [[Segmentação]]
