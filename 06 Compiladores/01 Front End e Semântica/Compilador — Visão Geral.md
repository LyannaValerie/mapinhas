---
title: Compilador — Visão Geral
tags:
  - compiladores
  - programação
aliases:
  - compiler overview
  - visão geral de compiladores
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Compiladores]]

# Compilador — Visão Geral

**Definição:** programa que traduz outro programa de uma linguagem fonte para uma linguagem alvo.

> [!info] Fonte
> Engineering a Compiler — Cooper & Torczon, 2012 (Cap. 1)

## O que um compilador precisa entender

Para traduzir, o compilador precisa entender:
- **Sintaxe** — forma do programa fonte
- **Semântica** — significado do programa fonte
- Regras da linguagem alvo

## Compilador vs Interpretador

| Aspecto | Compilador | Interpretador |
|---|---|---|
| Saída | programa traduzido | resultado direto |
| Execução | em tempo separado | linha a linha |
| Análise | compartilhada (parsing, type check) | compartilhada |

Compiladores JIT (*just-in-time*) combinam os dois: interpretam bytecode e compilam trechos quentes para código nativo.

## Estrutura em fases

Ver [[Compilador — Estrutura de Fases]].

Três fases principais:
1. [[Front End do Compilador]] — entende o fonte, produz [[Representação Intermediária (IR)]]
2. [[Otimizador de Compilador]] — melhora o IR
3. [[Back End do Compilador]] — mapeia IR para a máquina alvo

## ILOC

Linguagem intermediária usada como exemplo ao longo do livro.
- Assembly para uma máquina RISC simples hipotética
- Sigla: *Intermediate Language for an Optimizing Compiler*
- A maioria das operações usa registradores como argumentos

## Por que estudar compiladores

Aplica técnicas de toda a CC:
- algoritmos gulosos (register allocation)
- busca heurística (instruction selection)
- autômatos finitos e de pilha ([[Scanner — Análise Léxica|scanner]], [[Parser — Análise Sintática|parser]])
- programação dinâmica (instruction selection)
- grafos (data-flow, dependência)
