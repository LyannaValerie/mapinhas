---
title: Paralelismo em Nível de Instrução
aliases:
  - Instruction-Level Parallelism
  - ILP
tags:
  - computação/arquitetura
date: 2026-04-22
source: desconhecida
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Paralelismo em Nível de Instrução

Capacidade de um processador explorar sobreposição entre instruções independentes de um mesmo fluxo, reduzindo CPI efetivo e aumentando IPC.

## Relações (SPO)
- Paralelismo em Nível de Instrução → depende de → independência entre instruções
- ILP → é limitado por → hazards de dados, controle e estrutura
- [[Pipeline]] → expõe → oportunidades e custos de ILP
- [[Previsão de Desvio]] → reduz → stalls de controle
- [[Escalonamento Dinâmico]] → reordena → execução para esconder latência
- [[Especulação Baseada em Hardware]] → amplia → ILP além de dependências de controle

## Intuição

Em um bloco básico, o ILP disponível costuma ser pequeno porque branches aparecem com frequência e muitas instruções dependem de resultados anteriores. Ganho relevante exige combinar:

- pipeline com forwarding
- técnicas de compilador, como reordenação e unrolling
- execução dinâmica com renaming
- especulação sobre desvios
- emissão múltipla por ciclo

## Limitações principais

| Limite | Efeito |
|---|---|
| Dependência RAW | impede execução antes do produtor |
| Dependências WAR/WAW | criam falsos conflitos sem renaming |
| Branches frequentes | encurtam janela útil entre desvios |
| Falta de unidades funcionais | introduz hazard estrutural |
| Latência de memória | reduz paralismo observável |

## Métricas úteis

- **CPI ideal**: limite inferior sem stalls.
- **IPC**: instruções concluídas por ciclo.
- **Janela de instruções**: quantidade de instruções visíveis para reordenação.

Quanto maior a largura de emissão e a janela efetiva, maior potencial de ILP, mas também maior custo de hardware e energia.

## Ver também
- [[Pipeline]]
- [[Execução Fora de Ordem]]
- [[Escalonamento Dinâmico]]
- [[Previsão de Desvio]]
- [[Multithreading no Chip]]

criado pelo Codex
