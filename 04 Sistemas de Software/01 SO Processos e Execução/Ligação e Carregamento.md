---
title: Ligação e Carregamento
aliases:
  - linking and loading
  - linkagem e carregamento
tags:
  - computação/sistemas
source: "Linking and Loading"
created_by: Codex
---

# Ligação e Carregamento

criado pelo Codex

## Definição

**Ligação e carregamento** é o fluxo que transforma módulos compilados em uma imagem executável posicionada no espaço de endereços do processo.

## Relações (SPO)

- [[Ligador]] → combina → [[Arquivo Objeto]]
- [[Carregador]] → posiciona → imagem executável em memória
- [[Relocação de Código]] → ajusta → referências dependentes de endereço
- [[Símbolos de Ligação]] → conectam → definições e referências externas
- [[Biblioteca de Código Objeto]] → fornece → módulos reutilizáveis para ligação
- [[Ligação Dinâmica]] → adia → parte da resolução para load time ou runtime

## Pipeline

1. Compilador gera arquivos objeto relocáveis.
2. Ligador resolve símbolos, seleciona bibliotecas e calcula layout.
3. Saída vira executável ou biblioteca compartilhada.
4. Carregador mapeia segmentos, aplica relocação restante e transfere controle ao ponto de entrada.

## Ver também

- [[Formato ELF]]
- [[PIC e GOT]]
- [[ABI]]

