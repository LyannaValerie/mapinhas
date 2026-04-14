---
title: Memória Virtual
aliases:
  - virtual memory
  - espaço de endereçamento virtual
  - virtual address space
  - heap
  - pilha de programa
  - stack
tags:
  - computação/arquitetura
date: 2026-04-06
---

# Memória Virtual

## Definição
Abstração do [[Sistema Operacional]] que fornece a cada [[Processo]] a ilusão de que possui **uso exclusivo de toda a memória principal**. Cada processo enxerga a mesma visão uniforme da memória, chamada de **espaço de endereçamento virtual**.

## Relações (SPO)
- Memória Virtual → fornece → ilusão de memória exclusiva para cada [[Processo]]
- Memória Virtual → abstrai → [[Memória]] principal + disco
- Memória Virtual → implementada por → hardware (tradução de endereços) + SO
- Memória Virtual → organizada em → regiões com propósitos distintos

---

## O espaço de endereçamento virtual (Linux/x86-64)

O espaço de endereços é organizado em regiões bem definidas, do endereço **mais baixo** (0) ao **mais alto**:

```
Endereço alto  ┌─────────────────────────────┐
               │  Memória virtual do kernel  │  ← invisível ao código de usuário
               ├─────────────────────────────┤
               │  Pilha de usuário (stack)   │  ↕ cresce/diminui em tempo de execução
               ├─────────────────────────────┤
               │  Bibliotecas compartilhadas │  ex: libc.so, libm.so
               ├─────────────────────────────┤
               │  Heap                       │  ↕ cresce/diminui (malloc/free)
               ├─────────────────────────────┤
               │  Dados de leitura/escrita   │  variáveis globais C
               ├─────────────────────────────┤
               │  Código e dados (read-only) │  ← carregado do arquivo executável
Endereço 0     └─────────────────────────────┘
```

### Regiões em detalhe

| Região | Conteúdo | Tamanho |
|---|---|---|
| **Código e dados** | Instruções + variáveis globais C; carregados do executável | Fixo ao iniciar |
| **Heap** | Memória alocada dinamicamente (`malloc`/`free`) | Cresce/diminui em tempo de execução |
| **Bibliotecas compartilhadas** | Código/dados de bibliotecas como `libc` e `libm` | Variável |
| **Pilha (stack)** | Frames de função; cresce a cada chamada, diminui a cada retorno | Cresce/diminui dinamicamente |
| **Memória virtual do kernel** | Código e dados do kernel; inacessível ao código de usuário | Reservado |

> [!note] Por que o código está no endereço mais baixo?
> Por convenção — todos os processos começam com seu código no mesmo endereço fixo. Isso facilita a vinculação e carregamento pelo [[Sistema de Compilação|linker]].

---

## Implementação

A memória virtual funciona armazenando o conteúdo da memória virtual do processo em **disco**, usando a **[[Memória]] principal (RAM) como cache** para esse disco.

Toda vez que o [[Processador]] gera um endereço virtual, o hardware realiza uma **tradução automática** para o endereço físico real na RAM. Se a página não estiver na RAM (page fault), o SO a busca no disco.

> [!info] Interação hardware + SO
> A tradução de endereços requer cooperação sofisticada entre hardware (MMU — Memory Management Unit) e SO. É um dos mecanismos mais importantes dos sistemas modernos.

---

## Ver também
- [[Processo]] — cada processo tem seu próprio espaço de endereçamento virtual
- [[Sistema Operacional]] — memória virtual é uma das três abstrações fundamentais do SO
- [[Memória]] — a RAM é usada como cache para o disco na implementação da VM
- [[Hierarquia de Memória]] — VM estende a hierarquia até o disco
- [[Sistema de Compilação]] — código e dados são carregados do executável para as regiões fixas
- [[Paginação x86]] — mecanismo de hardware: tabelas PML4/PDPT/PD/PT, TLB, modos de paginação, XD/NX
- [[Modos de Operação x86]] — paginação e limites de endereçamento por modo
- [[Organização de Memória x86]] — segmentação, endereçamento linear/segmentado, alinhamento
