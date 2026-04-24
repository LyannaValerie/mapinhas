---
title: Compilador — Estrutura de Fases
tags:
  - compiladores
  - programação
aliases:
  - three-phase compiler
  - compilador três fases
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Compiladores]]

# Compilador — Estrutura de Fases

## Compilador de duas fases

```
Programa Fonte → [Front End] → IR → [Back End] → Programa Alvo
```

- Separa problemas da linguagem fonte (front) dos problemas da máquina alvo (back)
- Possibilita *retargeting*: trocar o back end para gerar código para outra máquina

## Compilador de três fases (otimizador)

```
Fonte → [Front End] → IR → [Otimizador] → IR → [Back End] → Alvo
```

- Inserir um [[Otimizador de Compilador|otimizador]] IR→IR causa mínima perturbação nos outros módulos
- Cada fase é subdividida em **passes** (passagens)

## Passes por fase

| Fase | Passes típicos |
|---|---|
| [[Front End do Compilador|Front End]] | scanner → parser → type checker → geração de IR |
| [[Otimizador de Compilador|Optimizer]] | 1..n otimizações sobre o IR |
| [[Back End do Compilador|Back End]] | instruction selection → register allocation → instruction scheduling |

## Infraestrutura compartilhada

Todas as fases compartilham estruturas de dados comuns (tabela de símbolos, representação do IR).

## Monolítico vs multipass (no otimizador)

| Abordagem | Vantagem |
|---|---|
| Monolítico | mais eficiente em execução |
| Multipass | mais simples de implementar e depurar; permite diferentes conjuntos de otimização |

## Retargeting e reutilização

- Múltiplos back ends para um único front end → mesmo fonte, várias máquinas
- Múltiplos front ends para um único back end → várias linguagens, mesma máquina

Ver [[Compilador — Visão Geral]].
