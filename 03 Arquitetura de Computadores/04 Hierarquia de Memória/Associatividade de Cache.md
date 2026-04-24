---
title: Associatividade de Cache
aliases:
  - cache associativa por conjuntos
  - n-way set associative
  - set associativity
tags:
  - computação/arquitetura
date: 2026-04-22
source: "Chapter Two Memory Hierarchy Design, seção 2.3"
created_by: "criado pelo Codex"
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Associatividade de Cache

## Definição
Propriedade que define **quantos blocos podem ocupar o mesmo conjunto** em uma [[Cache]].

## Relações (SPO)

- Associatividade de Cache → determina → flexibilidade de posicionamento de blocos
- Cache de mapeamento direto → possui → 1 bloco por conjunto
- Cache totalmente associativa → possui → 1 conjunto com todos os blocos
- Cache n-way set associative → possui → n blocos por conjunto
- maior associatividade → reduz → misses de conflito
- maior associatividade → tende a aumentar → hit time
- [[3 Cs de Cache]] → usa → conflito como categoria de miss

## Formas principais

| Organização | Estrutura | Efeito típico |
|---|---|---|
| **Mapeamento direto** | 1 bloco por conjunto | hit rápido, mais colisões |
| **Associativa por conjuntos** | n blocos por conjunto | equilíbrio entre custo e taxa de miss |
| **Totalmente associativa** | bloco pode ir a qualquer posição | menos colisões, hardware mais caro |

## Regra de mapeamento

No modelo por conjuntos, o bloco primeiro é mapeado para um conjunto e depois pode ocupar qualquer via daquele conjunto:

$$
conjunto = endereço\\ do\\ bloco \\bmod número\\ de\\ conjuntos
$$

## Trade-off central

- associatividade maior reduz conflito;
- associatividade maior exige comparação com mais tags;
- por isso, reduzir miss rate pode custar mais latência no hit.

## Leitura operacional

- [[Cache]] L1 costuma limitar associatividade para preservar hit time;
- *way prediction* busca recuperar parte do benefício de associatividade com menor custo de acesso;
- associatividade não elimina misses de capacidade, só atua principalmente sobre conflito.

## Ver também

- [[Cache]]
- [[3 Cs de Cache]]
- [[Otimizações Avançadas de Cache]]
