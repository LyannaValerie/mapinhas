---
title: Otimizações Avançadas de Cache
aliases:
  - Ten Advanced Optimizations of Cache Performance
  - otimizações de desempenho de cache
tags:
  - computação/arquitetura
date: 2026-04-22
source: "Chapter Two Memory Hierarchy Design, seção 2.3"
created_by: "criado pelo Codex"
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Otimizações Avançadas de Cache

## Definição
Conjunto de técnicas usadas para melhorar desempenho de [[Cache]] atuando sobre **hit time**, **miss rate**, **miss penalty**, **largura de banda** e, em alguns casos, **consumo de energia**.

## Relações (SPO)

- Otimizações Avançadas de Cache → reduzem → hit time, miss rate ou miss penalty
- Otimizações Avançadas de Cache → podem aumentar → complexidade de hardware
- Otimizações Avançadas de Cache → podem aumentar → consumo de energia
- [[Hierarquia de Memória]] → condiciona → trade-offs de otimização de cache
- [[Associatividade de Cache]] → interage com → hit time e conflict misses
- [[Políticas de Escrita em Cache]] → interagem com → miss penalty

## Famílias de otimização

| Família | Objetivo principal | Exemplos |
|---|---|---|
| Redução de **hit time** | acelerar acesso bem-sucedido | cache pequena e simples, *way prediction* |
| Redução de **miss penalty** | diminuir custo de cada falta | cache multinível, *critical word first*, prioridade de leitura, fusão de *write buffers* |
| Redução de **miss rate** | diminuir frequência de faltas | maior associatividade, otimizações de compilador |
| Aumento de **largura de banda** | sustentar mais acessos simultâneos | cache pipelineada, multibancada, não bloqueante |
| Uso de **paralelismo** | antecipar ou sobrepor transferências | *hardware prefetching*, *compiler prefetching* |

## Mecanismos relevantes

### Way prediction
Prevê qual via de um conjunto será acessada no próximo hit para aproximar o tempo de acesso de uma cache direta sem abandonar associatividade maior.

### Cache multinível
Insere níveis adicionais entre L1 e memória principal para aliviar o conflito entre cache pequena e rápida versus cache grande e lenta.

### Critical word first
Entrega primeiro a palavra requisitada em um miss, antes da linha inteira, reduzindo latência observada pelo processador.

### Prioridade de leitura sobre escrita
Com **write buffer**, processadores podem despachar leitura antes de escritas pendentes, desde que não haja conflito, para reduzir *miss penalty*.

### Virtual index / physical tag
Usa o *page offset* para indexar L1 antes do término da tradução virtual→física, retirando o [[TLB]] do caminho crítico ao custo de restrições estruturais.

### Cache não bloqueante
Permite continuar execução ou manter múltiplos misses pendentes, aumentando largura de banda efetiva.

### Prefetching
Antecipação de blocos por hardware ou compilador. Pode reduzir penalidade aparente, mas pode desperdiçar energia e tráfego quando a predição erra.

## Leitura operacional

- não existe otimização isolada universal;
- ganhos em uma métrica podem piorar outra;
- otimização de cache é problema de engenharia de trade-off, não lista de melhorias sempre positivas.

## Ver também

- [[Cache]]
- [[Hierarquia de Memória]]
- [[Associatividade de Cache]]
- [[Políticas de Escrita em Cache]]
- [[Memória Virtual]]
- [[TLB]]
