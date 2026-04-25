---
title: Entrada de Tabela de Páginas
aliases:
  - PTE
  - page table entry
tags:
  - computação/sistemas
  - sistemas-operacionais/memória
source: "Sistemas Operacionais Modernos, cap. 3"
created_by: Codex
---

# Entrada de Tabela de Páginas

Nota criada pelo Codex.

## Definição

Registro individual da [[Tabela de Páginas]] associado a uma página virtual.

## Campos típicos

- Quadro de página: identifica o quadro físico quando a página está presente.
- Bit de presente: indica se a página está na RAM.
- Bits de proteção: controlam leitura, escrita e execução.
- Bit de modificada: indica escrita desde a carga.
- Bit de referenciada: indica acesso recente.
- Controle de cache: permite tratar regiões mapeadas para dispositivos.

## Uso dos bits pelo hardware e SO

| Bit | Quem usa | Para quê |
|---|---|---|
| Presente | MMU | Se 0, gera [[Falha de Página]] |
| Proteção | MMU | Impede acesso indevido (ex: escrita em página RO) → trap |
| Modificada (*dirty*) | SO | Se 1 ao substituir, página deve ser salva no disco antes de reutilizar o quadro |
| Referenciada | SO | Bits de referência são zerados periodicamente; usados pelo [[Algoritmo de Substituição de Páginas]] para detectar páginas frias |
| Cache disable | MMU/hardware | Para regiões mapeadas a registradores de dispositivo — impede caching de leituras/escritas |

O campo *quadro de página* só é válido quando bit de presente = 1. Quando a página está no disco, o SO pode usar esse campo para armazenar o endereço no disco onde a página está guardada (comportamento definido pelo SO, não pelo hardware).

## Relações (SPO)

- Entrada de Tabela de Páginas -> descreve -> página virtual
- Entrada de Tabela de Páginas -> aponta para -> quadro físico
- bit de presente falso -> dispara -> [[Falha de Página]]
- bit de referenciada -> alimenta -> [[Algoritmo de Substituição de Páginas]]

