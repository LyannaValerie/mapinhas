---
title: MMU
aliases:
  - Memory Management Unit
  - Unidade de Gerenciamento de Memória
tags:
  - computação/sistemas
  - sistemas-operacionais/memória
source: "Sistemas Operacionais Modernos, cap. 3"
created_by: Codex
---

# MMU

Nota criada pelo Codex.

## Definição

Componente de hardware que traduz endereços virtuais emitidos pela CPU em endereços físicos de [[Memória]].

## Papel técnico

A MMU aplica o mapeamento definido pela [[Tabela de Páginas]], consulta a [[TLB]] quando disponível e sinaliza violações ou ausências que resultam em [[Falha de Página]].

## Funcionamento

A MMU fica entre a CPU e o barramento de memória. Endereços virtuais gerados pela CPU nunca chegam diretamente ao barramento; a MMU os intercepta e produz endereços físicos.

**Fluxo de tradução (paginação):**

1. CPU envia endereço virtual V à MMU.
2. MMU divide V em `[página_virtual | deslocamento]`.
3. Verifica [[TLB]]: se encontrar `(página_virtual → quadro)` → endereço físico = `[quadro | deslocamento]`.
4. Se não encontrar na TLB: busca entrada na [[Tabela de Páginas]].
   - Bit de presente = 1 → carrega na TLB, retorna endereço físico.
   - Bit de presente = 0 → sinaliza [[Falha de Página]] ao SO.
   - Violação de proteção (escrita em página somente leitura) → trap de proteção.
5. Endereço físico vai ao barramento de memória.

A MMU hoje integra chip da CPU, mas logicamente poderia ser separada (era separada nos processadores antigos).

## Relações (SPO)

- MMU -> traduz -> endereços virtuais
- MMU -> consulta -> [[TLB]]
- MMU -> usa -> [[Tabela de Páginas]]
- MMU -> detecta -> [[Falha de Página]]
- [[Paginação em Sistemas Operacionais]] -> depende de -> MMU

