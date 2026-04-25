---
title: Documentos de Domínio
aliases:
  - domain documents
  - domínio do problema
  - problem domain
tags:
  - computação/sistemas
source: "Tu, Do Hoang. Operating Systems: From 0 to 1"
created_by: Codex
---

# Documentos de Domínio

%% criado pelo Codex %%

## Definição

Um **domínio do problema** é a parte do mundo externo ao programa em que a máquina deve produzir efeitos. Inclui entidades controladas diretamente pelo software, como teclado, impressora, monitor ou outro programa, e entidades afetadas indiretamente por esses efeitos.

## Função

Documentos de domínio reduzem erro de implementação porque separam conhecimento do problema de detalhes de código. Programar não basta: o engenheiro precisa entender o domínio que o software pretende alterar.

## Relações

- [[Documento de Requisitos de Software]] descreve efeitos esperados no domínio.
- [[Documento de Especificação de Software]] descreve a interface e as restrições que conectam o programa ao domínio.
- [[Software]] materializa regras do domínio em comportamento executável.

