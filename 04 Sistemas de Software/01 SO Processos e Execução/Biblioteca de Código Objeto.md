---
title: Biblioteca de Código Objeto
aliases:
  - object code library
  - biblioteca estática
  - archive library
  - static library
tags:
  - computação/sistemas
source: "Linking and Loading"
created_by: Codex
---

# Biblioteca de Código Objeto

criado pelo Codex

## Definição

**Biblioteca de código objeto** é um agrupamento de módulos objeto reutilizáveis pesquisado pelo [[Ligador]] para satisfazer símbolos externos.

## Relações (SPO)

- Biblioteca estática → contém → múltiplos [[Arquivo Objeto]]
- [[Ligador]] → extrai → apenas módulos necessários
- [[Símbolos de Ligação]] → guiam → seleção de membros da biblioteca
- [[Ligação Dinâmica]] → substitui parcialmente → cópia estática por vínculo compartilhado

## Comportamento de busca

Bibliotecas costumam ser pesquisadas em ordem. Quando uma referência indefinida encontra uma definição em um membro da biblioteca, esse membro entra no link. Esse detalhe torna ordem de argumentos relevante em toolchains tradicionais.

## Trade-off

Ligação estática simplifica distribuição, mas duplica código em cada executável. Ligação dinâmica reduz duplicação e permite atualização compartilhada, mas adiciona dependência de runtime.

