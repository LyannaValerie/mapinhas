---
title: História da Linguagem C
aliases:
  - história de C
  - origem da linguagem C
  - C Through the Mists of Time
tags:
  - computação/programação/c
  - computação/histórico
date: 2026-04-24
source: fonte bibliográfica não confirmada
---

%% Nota criada pelo Codex %%
%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Programação]]

# História da Linguagem C

## Definição
História técnica da [[Linguagem C]] como linguagem de programação de sistemas surgida no ambiente Unix da Bell Labs, em reação prática à complexidade de sistemas grandes como Multics.

## Relações (SPO)
- Multics → influencia indiretamente → Unix
- Unix → impulsiona → [[Linguagem C]]
- Ken Thompson → cria → linguagem B
- Dennis Ritchie → cria → [[Linguagem C]]
- [[Linguagem C]] → descende de → BCPL e B
- [[Linguagem C]] → viabiliza → portabilidade do Unix

## Origem
O contexto inicial é a retirada de pesquisadores da Bell Labs do projeto Multics. O contraste técnico é importante: Multics acumulava soluções de engenharia, mas também demonstrava o custo de tentar construir um sistema operacional grande demais para o hardware disponível.

Ken Thompson, sem aprovação formal para um novo sistema operacional, explorou trabalho próprio em ambiente menor. A linguagem B apareceu nesse contexto como simplificação de BCPL para uso prático no Unix inicial.

## Linhagem
```text
BCPL
  -> B
    -> C
```

BCPL e B operavam com modelo mais próximo da palavra de máquina. C adicionou um sistema de tipos mais rico, preservando proximidade com hardware, memória e representação binária.

## Sucesso técnico
O sucesso de C não vem de pureza teórica. Vem da combinação entre:
- linguagem pequena
- baixo custo de compilação
- acesso direto a memória e operações de máquina
- portabilidade suficiente para reimplementar e portar Unix
- biblioteca e ferramentas que cresceram junto com o ecossistema Unix

## Ver também
- [[Linguagem C]]
- [[Sistema Operacional]]
- [[Sistema de Compilação]]
- [[Ponteiros em C]]
- [[Tipos de Dados C]]
