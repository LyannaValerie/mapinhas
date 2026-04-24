---
title: Tradução e Interpretação
aliases:
  - tradução
  - interpretação
  - compilação
  - intérprete
  - interpretador
tags:
  - computação/arquitetura
date: 2026-04-06
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Tradução e Interpretação

## Definição
Dois métodos para executar programas escritos numa linguagem de nível superior (L1) em um computador que só executa diretamente a [[Linguagem de Máquina]] (L0).

## Relações (SPO)
- Tradução → converte → programa L1 inteiro para L0 antes da execução
- Interpretação → executa → instruções L1 uma a uma via programa L0
- Tradução e Interpretação → resolvem → lacuna entre [[Linguagem de Máquina]] e linguagens de alto nível
- Tradução e Interpretação → fundamentam → [[Níveis de Abstração]]

## Comparação

|                                | Tradução                            | Interpretação                                |
| ------------------------------ | ----------------------------------- | -------------------------------------------- |
| O que gera                     | Novo programa em L0                 | Nenhum programa novo                         |
| Momento da conversão           | Antes da execução (todo o programa) | Durante a execução (instrução por instrução) |
| Quem controla a execução       | O programa L0 gerado                | O interpretador (programa em L0)             |
| O programa L1 durante execução | Desconsiderado                      | Tratado como dados                           |

> [!info] Semelhança fundamental
> Em ambos os métodos, o computador executa instruções L1 executando sequências equivalentes em L0. A diferença é **quando** e **como** a conversão ocorre.

## Tradução em detalhe
1. O programa L1 inteiro é convertido para um programa equivalente em L0
2. O programa L1 original é descartado
3. O novo programa L0 é carregado na [[Memória]] e executado diretamente

## Interpretação em detalhe
1. O interpretador (programa em L0) lê cada instrução L1
2. Para cada instrução, executa imediatamente a sequência equivalente em L0
3. Nenhum programa L0 é gerado — o interpretador permanece no controle
4. O programa L1 é tratado como dados de entrada

> [!note] Na prática
> Computadores modernos utilizam frequentemente uma **combinação** dos dois métodos (ex: compilação para bytecode + interpretação/JIT).

## Ver também
- [[Linguagem de Máquina]]
- [[Níveis de Abstração]]
- [[Programa Armazenado]]
- [[Software]]
- [[Sistema de Compilação]] — instância concreta de tradução no pipeline C/Unix
