---
title: Interface de Mouse DOS
aliases:
  - INT 33h
  - mouse driver DOS
  - MOUSE.COM
tags:
  - computação/sistemas
  - computação/entrada
date: 2026-04-24
source: "PC System Programming — Abacus, cap. 14"
codex_note: criado pelo Codex
---

# Interface de Mouse DOS

## Definição

A interface de mouse DOS é uma API de driver, compatível com o padrão Microsoft Mouse, chamada por `INT 33h`. Ela costuma ser instalada por driver de dispositivo ou por TSR como `MOUSE.COM`.

## Convenção de chamada

O número da função é passado em `AX`. Demais registradores carregam parâmetros e retornos conforme a função chamada.

Funções típicas:

- `00h`: reinicia driver e detecta mouse;
- `01h`: mostra ponteiro;
- `02h`: remove ponteiro;
- `04h`: move ponteiro para coordenadas específicas;
- `07h`: limita faixa horizontal;
- `08h`: limita faixa vertical.

Após `00h`, `AX = 0000h` indica driver ausente; `AX = FFFFh` indica mouse/driver presente. `BX` retorna quantidade de botões.

## Coordenadas e ponteiro

A interface interpreta posição em uma tela gráfica virtual. Em modo texto, coordenadas de coluna/linha precisam ser convertidas para coordenadas gráficas, normalmente multiplicando por 8 ou deslocando três bits à esquerda.

O ponteiro em modo texto resulta da combinação entre máscara de tela e máscara de cursor aplicada aos bytes de caractere e atributo na RAM de vídeo.

## Interação com saída direta

Programas que escrevem diretamente na RAM de vídeo devem remover o ponteiro antes de atualizações extensas. Se o programa sobrescreve a célula onde o ponteiro está, o driver pode restaurar caractere antigo quando o ponteiro se mover.

## Relações (SPO)

- [[Interface de Mouse DOS]] usa [[Interrupção]] `33h`.
- [[Interface de Mouse DOS]] depende de [[Driver de Dispositivo DOS]] ou [[Programa TSR DOS]].
- [[Interface de Mouse DOS]] abstrai [[Mouse]] para programas DOS.
- [[Interface de Mouse DOS]] interage com [[Acesso Direto à RAM de Vídeo]].

## Ver também

- [[Mouse]]
- [[MS-DOS]]
- [[Programa TSR DOS]]
- [[Acesso Direto à RAM de Vídeo]]
