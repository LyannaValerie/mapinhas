---
title: Tabela de Páginas
aliases:
  - page table
tags:
  - computação/sistemas
  - sistemas-operacionais/memória
source: "Sistemas Operacionais Modernos, cap. 3"
created_by: Codex
---

# Tabela de Páginas

Nota criada pelo Codex.

## Definição

Estrutura de dados que mapeia páginas virtuais de um [[Espaço de Endereçamento]] para quadros de [[Memória]] física.

## Papel técnico

A tabela de páginas é consultada durante a tradução de endereços. Ela também carrega metadados de presença, proteção, modificação e referência, usados tanto pela [[MMU]] quanto por políticas do [[Sistema Operacional]].

## Funcionamento

**Tabela linear (nível único):** uma entrada por página virtual. Para espaço de 64 KB com páginas de 4 KB → 16 entradas. Para 32 bits (4 GB) com páginas de 4 KB → 1 milhão de entradas por processo — muito grande.

**Tabela multinível:** divide o número de página em dois (ou mais) campos. O primeiro campo indexa uma *tabela de diretório de páginas*; cada entrada do diretório aponta para uma tabela de segundo nível. Entradas de diretório inválidas dispensam a tabela de segundo nível correspondente → economia de memória para espaços esparsos.

**Tabela invertida:** uma entrada por quadro físico (não por página virtual). Cada entrada contém `(pid, número de página virtual)` do processo que ocupa aquele quadro. Busca por hash de `(pid, página virtual)`. Escala melhor para espaços de 64 bits, mas busca mais complexa.

Independente da estrutura, a tabela reside na RAM. Por isso a [[TLB]] é essencial: sem ela, cada acesso à memória exigiria pelo menos duas referências à RAM (tabela + dado).

## Relações (SPO)

- Tabela de Páginas -> mapeia -> página virtual para quadro físico
- Tabela de Páginas -> contém -> [[Entrada de Tabela de Páginas]]
- [[TLB]] -> armazena cache de -> Tabela de Páginas
- [[Falha de Página]] -> consulta -> Tabela de Páginas
- [[Paginação em Sistemas Operacionais]] -> depende -> Tabela de Páginas

