---
title: E/S Mapeada na Memória
aliases:
  - entrada e saída mapeada na memória
  - memory-mapped I/O
  - MMIO
tags:
  - computação/sistemas
source: "Sistemas Operacionais Modernos, cap. 5"
created_by: Codex
---

# E/S Mapeada na Memória

Conteúdo criado pelo Codex.

## Definição

**E/S mapeada na memória** é a técnica em que registradores ou buffers de um [[Controlador de Dispositivo]] aparecem no espaço de endereçamento acessível pela CPU.

## Consequência

O sistema operacional usa operações de leitura e escrita em endereços especiais para comandar dispositivos. Isso evita instruções dedicadas de E/S, mas exige controle de cache, proteção e ordenação de acessos.

## Relações (SPO)

- E/S Mapeada na Memória -> mapeia -> registradores de dispositivo
- CPU -> acessa -> E/S Mapeada na Memória
- [[Kernel]] -> protege -> regiões de E/S Mapeada na Memória
- E/S Mapeada na Memória -> contrasta com -> espaço separado de E/S

