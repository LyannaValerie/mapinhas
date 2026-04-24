---
title: Análise Sensível ao Contexto
tags:
  - compiladores
  - semântica
aliases:
  - context-sensitive analysis
  - elaboração semântica
  - semantic elaboration
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Compiladores]]

# Análise Sensível ao Contexto

Fase do compilador que verifica erros que uma CFG não pode detectar: **erros de tipo** e **erros de acordo** (número de argumentos, dimensões de arrays, etc.).

> [!info] Contexto
> Cap. 4 de *Engineering a Compiler*. Vem após o [[Parser — Análise Sintática|parser]].

## Motivação

Um programa gramaticalmente correto pode ainda conter erros sérios:
- `a ← a × 2 × b × c` válido sintaticamente, mas inválido se `b` for string
- chamada com número errado de argumentos
- referência a variável não declarada

A análise sensível ao contexto usa **conhecimento contextual** derivado do código fonte.

## O que o Compilador Precisa Saber

- **Tipos**: o que é cada valor e como pode ser usado
- **Espaço de nomes**: o que cada nome representa em cada ponto do código
- **Fluxo de controle**: como valores fluem entre nomes
- **Interações externas**: I/O, arquivos, dispositivos

## Duas Técnicas Principais

| Técnica | Descrição |
|---|---|
| [[Gramáticas de Atributos]] | formalismo funcional; regras associadas a produções; avaliação por grafo de dependências |
| [[Tradução Dirigida por Sintaxe — Ad Hoc]] | snippets de código associados a produções; executados em tempo de parsing |

## Elaboração Semântica

Termo que engloba todas as tarefas feitas nessa fase:
- construção da [[Tabela de Símbolos]]
- verificação de tipos ([[Sistemas de Tipos]])
- inserção de conversões implícitas (coerções)
- resolução de sobrecarga de operadores
- geração de IR anotada com informações de tipo

## Abstrações Usadas

O compilador constrói visões alternativas do programa além da sintaxe:
- **Sistema de tipos**: verifica consistência de tipos
- **Mapa de armazenamento**: associa nomes a localizações
- **CFG**: modela fluxo de controle (construída na geração de IR)

## Posição no Pipeline

```
Tokens → [Parser] → Parse Tree/AST
                          ↓
            [Análise Sensível ao Contexto]
                          ↓
              IR anotada com tipos e escopo
```

## Relações

- [[Front End do Compilador]] — fase que contém esta análise
- [[Sistemas de Tipos]] — principal abstração usada
- [[Gramáticas de Atributos]] — formalismo
- [[Tradução Dirigida por Sintaxe — Ad Hoc]] — implementação prática
- [[Tabela de Símbolos]] — repositório central de informação
- [[Representação Intermediária (IR)]] — saída desta fase
