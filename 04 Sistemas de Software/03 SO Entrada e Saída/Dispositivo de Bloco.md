---
title: Dispositivo de Bloco
aliases:
  - dispositivos de bloco
  - block device
tags:
  - computação/sistemas
source: "Sistemas Operacionais Modernos, cap. 5"
created_by: Codex
---

# Dispositivo de Bloco

Conteúdo criado pelo Codex.

## Definição

**Dispositivo de bloco** é um dispositivo de E/S que armazena dados em blocos de tamanho fixo, cada um com endereço próprio.

## Propriedade central

Cada bloco pode ser lido ou escrito independentemente dos demais. Essa propriedade permite acesso aleatório e modelagem por arquivos, setores e buffers.

## Exemplos

- [[Disco Rígido]]
- [[SSD]]
- pendrive
- disco óptico

## Relações (SPO)

- Dispositivo de Bloco -> transfere -> blocos de dados
- Dispositivo de Bloco -> permite -> acesso aleatório
- [[Driver]] -> encapsula -> Dispositivo de Bloco
- [[Software de E-S Independente de Dispositivo|Software de E/S Independente de Dispositivo]] -> uniformiza -> Dispositivo de Bloco
