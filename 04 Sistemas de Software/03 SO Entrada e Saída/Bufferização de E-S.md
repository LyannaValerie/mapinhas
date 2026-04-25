---
title: Bufferização de E/S
aliases:
  - buffering de E/S
  - buffer de E/S
  - I/O buffering
tags:
  - computação/sistemas
source: "Sistemas Operacionais Modernos, cap. 5"
created_by: Codex
---

# Bufferização de E/S

Conteúdo criado pelo Codex.

## Definição

**Bufferização de E/S** é o uso de memória intermediária para desacoplar ritmo, tamanho e momento das transferências entre processo, kernel e dispositivo.

## Motivo

Dispositivos e processos raramente operam na mesma velocidade ou no mesmo tamanho de unidade. Buffers reduzem bloqueios, absorvem rajadas e permitem que o kernel reorganize transferências.

## Relações (SPO)

- Bufferização de E/S -> usa -> [[Memória]]
- Bufferização de E/S -> reduz -> acoplamento temporal
- [[Software de E-S Independente de Dispositivo|Software de E/S Independente de Dispositivo]] -> implementa -> Bufferização de E/S
- Bufferização de E/S -> ajuda -> [[Dispositivo de Caractere]]
- Bufferização de E/S -> ajuda -> [[Dispositivo de Bloco]]
