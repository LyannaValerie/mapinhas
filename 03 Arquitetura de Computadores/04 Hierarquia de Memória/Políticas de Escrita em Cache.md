---
title: Políticas de Escrita em Cache
aliases:
  - write-through
  - write-back
  - write buffer
tags:
  - computação/arquitetura
date: 2026-04-22
source: "Chapter Two Memory Hierarchy Design, seção 2.3"
created_by: "criado pelo Codex"
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Políticas de Escrita em Cache

## Definição
Conjunto de estratégias para manter consistência entre [[Cache]] e memória principal durante operações de escrita.

## Relações (SPO)

- Write-through → atualiza → cache e memória principal no mesmo write
- Write-back → atualiza imediatamente → apenas cache
- Write-back → grava memória principal quando → bloco é substituído
- Write buffer → desacopla → latência de escrita do avanço da cache
- prioridade para leituras → reduz → miss penalty em presença de writes pendentes
- [[Coerência de Cache]] → torna relevante → visibilidade correta de writes

## Estratégias principais

| Política | Comportamento | Trade-off |
|---|---|---|
| **Write-through** | escreve em cache e memória principal | simples, mais tráfego |
| **Write-back** | escreve primeiro na cache; memória recebe dado no despejo | menos tráfego, mais complexidade |

## Write buffer

Ambas as estratégias podem usar **write buffer** para permitir que a cache prossiga assim que dado entra no buffer, sem esperar a escrita completa na memória.

## Hazard importante

Write buffer pode introduzir dependência **read-after-write through memory**:

- leitura em miss pode precisar de valor ainda pendente no buffer;
- verificar conflitos no buffer antes de despachar leitura evita ler valor desatualizado;
- se não houver conflito e memória estiver disponível, priorizar leitura reduz penalidade.

## Leitura antes de escrita

Muitos processadores dão **prioridade a read misses sobre writes** para reduzir *miss penalty*.

## Leitura operacional

- **write-through** simplifica consistência, mas amplia tráfego de memória;
- **write-back** reduz tráfego, mas exige controle de linha modificada e coerência;
- **write buffer** melhora vazão, mas introduz hazard de leitura após escrita pendente.

## Ver também

- [[Cache]]
- [[DMA]]
- [[Coerência de Cache]]
- [[Otimizações Avançadas de Cache]]
