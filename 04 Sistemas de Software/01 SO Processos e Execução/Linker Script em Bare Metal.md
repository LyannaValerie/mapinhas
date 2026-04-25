---
title: Linker Script em Bare Metal
aliases:
  - linker script bare metal
  - script de ligação bare metal
tags:
  - computação/sistemas
  - computação/compiladores
source: "Operating Systems: From 0 to 1, cap. 8"
created_by: Codex
---

# Linker Script em Bare Metal

Nota criada pelo Codex.

## Definição

Um **linker script em bare metal** define como o [[Ligador]] deve combinar seções de arquivos objeto e posicioná-las na imagem final quando não há sistema operacional para fornecer layout padrão de execução.

## Uso

Em ambiente hospedado, `ld` usa um script padrão adequado ao sistema operacional. Em bare metal, o programador precisa declarar endereços, seções de saída e layout de memória esperado pelo bootloader ou pelo hardware.

## Relações (SPO)

- Linker script → instrui → [[Ligador]]
- Linker script → organiza → seções ELF
- Bare metal → exige → layout explícito de memória
- [[Formato ELF]] → contém → seções e cabeçalhos usados pelo ligador

