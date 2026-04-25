---
title: Gerenciamento de Memória no Windows
aliases:
  - memória virtual no Windows
  - gerenciador de memória do Windows
  - section object
tags:
  - computação/sistemas
  - sistemas-operacionais/windows
  - sistemas-operacionais/memória
source: "Sistemas Operacionais Modernos, cap. 11"
created_by: Codex
---

# Gerenciamento de Memória no Windows

## Definição

Sistema de [[Memória Virtual em Sistemas Operacionais|memória virtual]] do Windows, implementado pelo gerenciador de memória na camada executiva NTOS.

## Espaço de endereçamento virtual

Cada processo possui espaço virtual privado. Em x86 de 32 bits: 2 GB para usuário, 2 GB para núcleo (por padrão). Em 64 bits: 128 TB para cada.

Regiões do espaço virtual são descritas pela **VAD (Virtual Address Descriptor) tree**: árvore balanceada que registra cada região alocada com tipo (privada, mapeada, imagem), proteção e estado (reservada, submetida, livre).

## Section objects (seções)

**Section object** = objeto do núcleo que representa um arquivo ou área de swap que pode ser mapeado em espaços de endereçamento. Permite:
- Arquivos mapeados em memória (`CreateFileMapping` + `MapViewOfFile` no Win32)
- Memória compartilhada entre processos (seções nomeadas)
- Carregamento de executáveis e DLLs (imagens mapeadas)

O [[Cache do Windows]] também usa seções para mapear arquivos no espaço de endereçamento do núcleo.

## Faltas de página

O tratador de faltas de página verifica a VAD para decidir a ação:
- Página privada não alocada: aloca página zerada
- Página de arquivo mapeado: lê do arquivo (via gerenciador de cache ou E/S direta)
- Página copy-on-write: copia para página privada antes de escrever
- Acesso fora de região válida: `STATUS_ACCESS_VIOLATION`

## Working set

Conjunto de páginas físicas atribuídas ao processo. O gerenciador de memória ajusta o working set via algoritmos de substituição quando memória física fica escassa. Pode usar informação de referência por página.

## Relações (SPO)

- Gerenciamento de Memória no Windows -> implementa -> [[Memória Virtual em Sistemas Operacionais]]
- VAD tree -> descreve -> regiões do espaço virtual
- Section object -> mapeia -> arquivos ou memória compartilhada
- [[Cache do Windows]] -> depende de -> gerenciador de memória
- [[Processos e Threads no Windows]] -> usam -> espaço de endereçamento virtual

