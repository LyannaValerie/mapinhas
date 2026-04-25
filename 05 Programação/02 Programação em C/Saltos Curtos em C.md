---
title: Saltos Curtos em C
aliases:
  - goto em C
  - labels em C
  - salto local em C
tags:
  - computação/programação/c
date: 2026-04-24
source: desconhecida
created_by: "criado pelo Codex"
---

# Saltos Curtos em C

Saltos curtos em C usam `goto` e labels para transferir controle dentro da mesma função. O destino é léxico e local: um label normal não permite atravessar para outra função.

## Relações (SPO)

- [[Saltos Curtos em C]] — implementam — mudança local de controle
- `goto` — salta para — label da mesma função
- [[Limpeza com goto em C]] — usa — salto curto para saída excepcional e liberação de recursos
- [[Saltos Não-locais em C]] — generalizam — desvio para ponto salvo fora da função corrente

## Uso adequado

`goto` é defensável quando expressa saída excepcional, cleanup único ou colapso de erro em código C. Uso para fluxo algorítmico comum tende a obscurecer blocos básicos e invariantes locais.

## Restrições

- Não atravessa fronteira de função.
- Pode interagir mal com escopos que introduzem objetos automáticos.
- Não substitui `break`, `continue`, `return`, `if`, `switch` ou loops quando essas estruturas modelam melhor a intenção.

## Ver também

- [[Controle de Fluxo C]]
- [[Limpeza com goto em C]]
- [[Saltos Não-locais em C]]
