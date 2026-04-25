---
title: Dispositivo de Caractere
aliases:
  - dispositivos de caractere
  - character device
tags:
  - computação/sistemas
source: "Sistemas Operacionais Modernos, cap. 5"
created_by: Codex
---

# Dispositivo de Caractere

Conteúdo criado pelo Codex.

## Definição

**Dispositivo de caractere** é um dispositivo de E/S que entrega ou recebe fluxo sequencial de bytes, sem endereçamento independente por bloco.

## Papel técnico

Esse modelo serve para dispositivos cuja operação natural é sequencial ou orientada a eventos, como teclado, mouse, portas seriais e impressoras.

## Relações (SPO)

- Dispositivo de Caractere -> transfere -> fluxo de bytes
- Dispositivo de Caractere -> contrasta com -> [[Dispositivo de Bloco]]
- [[Driver]] -> controla -> Dispositivo de Caractere
- [[Bufferização de E-S|Bufferização de E/S]] -> absorve -> diferenças de ritmo do Dispositivo de Caractere
