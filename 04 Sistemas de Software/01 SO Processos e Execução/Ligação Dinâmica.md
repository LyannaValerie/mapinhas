---
title: Ligação Dinâmica
aliases:
  - dynamic linking
  - dynamic loading
  - ligação em tempo de carga
tags:
  - computação/sistemas
source: "Linking and Loading"
created_by: Codex
---

# Ligação Dinâmica

criado pelo Codex

## Definição

**Ligação dinâmica** resolve parte das dependências binárias quando o programa é carregado ou enquanto já está executando.

## Relações (SPO)

- [[Carregador]] → aciona → ligador dinâmico
- [[Formato ELF]] → armazena → metadados dinâmicos
- [[PIC e GOT]] → implementa → acesso relocável a dados e funções
- [[Relocação de Código]] → permanece → parcialmente pendente até load time
- biblioteca compartilhada → fornece → código comum para múltiplos processos

## Modos

- **load-time dynamic linking**: dependências são resolvidas durante carga do executável.
- **lazy binding**: chamada externa é resolvida na primeira execução.
- **runtime dynamic loading**: programa abre biblioteca explicitamente durante execução.

## Custo

Ligação dinâmica reduz duplicação de código e permite compartilhamento, mas adiciona tabela dinâmica, indireções e possibilidade de falha por versão ou ausência de biblioteca.

