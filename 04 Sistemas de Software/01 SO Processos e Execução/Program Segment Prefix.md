---
title: Program Segment Prefix
aliases:
  - PSP
  - prefixo de segmento de programa
tags:
  - computação/sistemas
date: 2026-04-24
created_by: Codex
source: desconhecida
---

# Program Segment Prefix

Nota criada pelo Codex.

## Definição

Program Segment Prefix é o bloco criado pelo [[MS-DOS]] antes da imagem de um programa carregado. Ele contém metadados usados pelo programa e pelo sistema durante execução e terminação.

## Relações (SPO)
- DOS → cria → PSP
- PSP → precede → [[Programa COM]]
- PSP → precede → [[Programa EXE]]
- PSP → contém → parâmetros de linha de comando
- PSP → referencia → bloco de ambiente
- Programa DOS → acessa → PSP por registradores de segmento

---

## Uso

Em programas EXE, o carregador ajusta referências de segmento e coloca `DS` e `ES` apontando para o PSP antes de iniciar o programa. Isso permite acesso a informações como ambiente e command tail.

Em programas COM, código, dados, pilha e PSP ficam no mesmo segmento. A simplicidade do formato reduz metadados, mas limita organização e controle de memória.

## Ver também
- [[MS-DOS]]
- [[Programa COM]]
- [[Programa EXE]]
- [[EXEC DOS]]

