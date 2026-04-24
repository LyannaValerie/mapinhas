---
title: Técnicas de Compilador para Expor ILP
aliases:
  - Basic Compiler Techniques for Exposing ILP
  - otimizações de compilador para ILP
tags:
  - computação/arquitetura
  - compiladores
date: 2026-04-22
source: desconhecida
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Técnicas de Compilador para Expor ILP

Conjunto de transformações estáticas que reorganizam o código para aumentar independência aparente entre instruções e permitir melhor uso de [[Pipeline]] e emissão múltipla.

## Relações (SPO)
- Compilador → reordena → instruções independentes
- Compilador → aplica → unrolling de laço
- Escalonamento estático → tenta esconder → latências conhecidas
- [[Previsão de Desvio]] → complementa → otimizações de branch
- Técnica estática → depende de → latências e microarquitetura assumidas

## Técnicas comuns

| Técnica | Objetivo | Custo |
|---|---|---|
| Reordenação de instruções | esconder latência sem mudar semântica | limitada por dependências |
| Loop unrolling | expor mais operações independentes | aumenta tamanho do código |
| Renomeação por compilador | reduzir conflitos de nome em registradores | aumenta pressão por registradores |
| Software pipelining | sobrepor iterações distintas do laço | compilação mais complexa |

## Quando funciona bem

- laços com acesso regular a memória
- dependências previsíveis
- latências relativamente estáveis
- código científico e vetorial

## Limitações

- não lida bem com ponteiros e aliasing ambíguo
- perde eficácia quando latência real diverge da prevista
- código binário distribuído para várias microarquiteturas tende a favorecer [[Escalonamento Dinâmico]]

## Exemplo conceitual

Sem reordenação:

```text
load
use
stall
stall
```

Com reordenação:

```text
load
operação independente
outra operação independente
use
```

## Ver também
- [[Paralelismo em Nível de Instrução]]
- [[Escalonamento Dinâmico]]
- [[Otimização de Código]]

criado pelo Codex
