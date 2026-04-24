---
title: Representação Intermediária (IR)
tags:
  - compiladores
  - programação
aliases:
  - IR
  - intermediate representation
  - representação intermediária
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Compiladores]]

# Representação Intermediária (IR)

Estrutura interna que o compilador usa para representar o programa durante a tradução.

> [!info] Papel
> IR é a interface entre [[Front End do Compilador|front end]], [[Otimizador de Compilador|otimizador]] e [[Back End do Compilador|back end]]. Seu significado é independente da linguagem fonte e da máquina alvo.

## Propriedades

- Em cada ponto da compilação, o compilador mantém uma **IR definitiva** — a versão canônica sendo processada
- Um compilador pode usar IRs diferentes em fases distintas
- A IR definitiva é a que transita entre fases independentes

## Tipos de IR

| Tipo | Descrição | Nota |
|---|---|---|
| Graphical | grafo (AST, CFG, DAG) | [[IR Graphical — Árvores e Grafos]] |
| Linear | assembly abstrato (three-address, stack) | [[IR Linear — Código de Três Endereços]] |
| SSA | linear + definição única por variável | [[Forma SSA]] |
| Híbrida | combina grafos e linear | — |

A escolha depende da linguagem fonte, da máquina alvo e das transformações aplicadas.

## ILOC como IR

[[Compilador — Visão Geral#ILOC|ILOC]] é a IR linear concreta usada como exemplo no livro:
- assembly de máquina RISC hipotética com registradores virtuais ilimitados
- cada bloco termina com branch/jump explícito (sem fall-through)
- back end faz register allocation real sobre ela

## Relações

- [[Front End do Compilador]] produz IR
- [[Otimizador de Compilador]] consome e emite IR (IR→IR)
- [[Back End do Compilador]] consome IR e emite código nativo
- [[Tabela de Símbolos]] — componente integral da IR
- [[Análise Sensível ao Contexto]] — anota IR com informações de tipo e escopo
