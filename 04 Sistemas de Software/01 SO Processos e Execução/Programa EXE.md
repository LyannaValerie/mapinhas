---
title: Programa EXE
aliases:
  - arquivo EXE
  - formato EXE DOS
tags:
  - computação/sistemas
date: 2026-04-24
created_by: Codex
source: desconhecida
---

# Programa EXE

Nota criada pelo Codex.

## Definição

Programa EXE é um formato executável do [[MS-DOS]] com cabeçalho capaz de descrever segmentos, pilha, ponto de entrada, tamanho de memória necessário e informação de relocação.

## Relações (SPO)
- Programa EXE → contém → cabeçalho
- Cabeçalho EXE → descreve → ponto de entrada
- Cabeçalho EXE → descreve → pilha inicial
- Carregador EXE → ajusta → referências de segmento
- Programa EXE → recebe → [[Program Segment Prefix]]
- Programa EXE → contrasta com → [[Programa COM]]

---

## Carregamento

O carregador resolve referências de segmento para os endereços correntes, posiciona registradores de segmento conforme o ambiente de execução e transfere controle para o ponto de entrada definido no cabeçalho.

Para compatibilidade, a terminação deve ocorrer por serviço de DOS, como `INT 21h` função `4Ch`, em vez de depender de retorno implícito.

## Ver também
- [[MS-DOS]]
- [[Program Segment Prefix]]
- [[Programa COM]]
- [[Ligador]]
- [[Processo de Montagem]]

