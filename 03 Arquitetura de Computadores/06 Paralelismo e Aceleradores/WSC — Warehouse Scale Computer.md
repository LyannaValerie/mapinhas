---
title: WSC — Warehouse Scale Computer
aliases:
  - Warehouse Scale Computer
  - WSC
  - datacenter em escala
tags:
  - computação/arquitetura
  - computação/classes
date: 2026-04-22
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# WSC — Warehouse Scale Computer

## Definição
Classe de computador formada por dezenas de milhares de servidores commodity interligados, operados por uma única organização dentro de um datacenter. Exemplos: infraestrutura do Google, Amazon AWS, Meta.

## Relações (SPO)
- WSC → é uma classe de → [[Classes de Computadores]]
- WSC → usa → hardware commodity em vez de servidores especializados
- WSC → trata → falhas como norma, não exceção
- WSC → otimiza para → disponibilidade e throughput agregado
- WSC → custo dominado por → energia e amortização de CAPEX
- [[Dependabilidade]] → aplicada em escala em → WSC

## Economia

| Item | Proporção do custo mensal |
|---|---|
| CAPEX (hardware amortizado em 3–4 anos) | ~50% |
| Energia elétrica | ~40% |
| Outros (rede, refrigeração, pessoal) | ~10% |

**Regra Watt-Ano (2011, América do Norte):** 1 Watt contínuo por ano ≈ **$2** de custo totalmente carregado (inclui infraestrutura de energia e resfriamento). Quantifica o valor de cada ganho de eficiência energética.

## Implicações de projeto
- Falhas de disco, servidor e rack são eventos rotineiros → software deve tolerar falhas
- Redundância implementada em software (replicação, checkpointing), não em hardware especializado
- Commodity hardware tem melhor custo/desempenho que servidores de alta confiabilidade
- PUE (*Power Usage Effectiveness*) = energia total / energia de TI — métrica de eficiência do datacenter

## Ver também
- [[Classes de Computadores]]
- [[Dependabilidade]]
- [[Custo de Fabricação de Chips]]
- [[Regras Práticas de Arquitetura]]
- [[Multiprocessadores]]
