---
title: 3 Cs de Cache
aliases:
  - modelo dos 3 Cs
  - misses compulsórios, de capacidade e de conflito
tags:
  - computação/arquitetura
date: 2026-04-22
source: "Chapter Two Memory Hierarchy Design, seção 2.3"
created_by: "criado pelo Codex"
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# 3 Cs de Cache

## Definição
Modelo que classifica faltas de cache (*cache misses*) em três classes causais: **compulsória**, **de capacidade** e **de conflito**.

## Relações (SPO)

- 3 Cs de Cache → classifica → faltas de cache
- Miss compulsório → ocorre em → primeiro acesso a um bloco
- Miss de capacidade → ocorre quando → conjunto de trabalho excede tamanho da cache
- Miss de conflito → ocorre quando → múltiplos blocos concorrem pelo mesmo conjunto
- [[Associatividade de Cache]] → reduz → misses de conflito
- [[Otimização de Código#Localidade e Cache]] → pode reduzir → misses de capacidade

## Classes

| Classe | Condição | Observação |
|---|---|---|
| **Compulsória** | primeiro acesso ao bloco | ocorre mesmo com cache infinita |
| **Capacidade** | dados úteis não cabem simultaneamente na cache | bloco é descartado e depois buscado de novo |
| **Conflito** | mapeamento força colisão no mesmo conjunto | típica em caches menos associativas |

## Leitura operacional

- Miss **compulsório** mede custo inevitável de popular cache vazia.
- Miss **de capacidade** indica pressão estrutural de working set.
- Miss **de conflito** indica limitação de mapeamento, não necessariamente falta de capacidade total.

## Ver também

- [[Cache]]
- [[Associatividade de Cache]]
- [[Hierarquia de Memória]]
- [[Otimizações Avançadas de Cache]]
