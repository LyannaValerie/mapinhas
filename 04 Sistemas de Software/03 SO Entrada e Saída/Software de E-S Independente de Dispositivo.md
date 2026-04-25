---
title: Software de E/S Independente de Dispositivo
aliases:
  - software independente de dispositivo
  - device-independent I/O software
tags:
  - computação/sistemas
source: "Sistemas Operacionais Modernos, cap. 5"
created_by: Codex
---

# Software de E/S Independente de Dispositivo

Conteúdo criado pelo Codex.

## Definição

**Software de E/S independente de dispositivo** é a camada que implementa funções comuns a várias classes de dispositivos e oferece interface uniforme para camadas superiores.

## Funções comuns

- uniformizar interfaces de [[Driver|drivers]]
- nomear dispositivos
- proteger acesso a dispositivos
- fazer [[Bufferização de E-S|Bufferização de E/S]]
- reportar erros
- alocar e liberar dispositivos dedicados
- abstrair tamanho de bloco

## Relações (SPO)

- Software de E/S Independente de Dispositivo -> fornece -> interface uniforme
- Software de E/S Independente de Dispositivo -> usa -> [[Driver]]
- Software de E/S Independente de Dispositivo -> implementa -> [[Bufferização de E-S|Bufferização de E/S]]
- Software de E/S Independente de Dispositivo -> esconde -> diferenças entre dispositivos
