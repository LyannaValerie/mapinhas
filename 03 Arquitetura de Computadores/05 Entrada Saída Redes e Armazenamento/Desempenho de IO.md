---
title: Desempenho de IO
aliases:
  - IO performance
  - I/O performance
  - throughput de IO
  - latência de IO
  - benchmark de IO
  - TPC
  - transaction processing
  - weakest link
tags:
  - computação/arquitetura
  - sistemas
  - io
  - desempenho
date: 2026-04-23
source: "P&H COD Cap. 8 §§8.6–8.7"
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Desempenho de IO

Desempenho de I/O depende de múltiplos componentes do sistema (dispositivo, controlador, barramento, memória, SO) e é medido de formas distintas conforme a aplicação.

## Métricas Básicas

| Métrica | Unidade | Foco |
|---|---|---|
| **I/O rate** | ops/s (IOPs) | Número de operações por segundo |
| **Data rate** | MB/s ou GB/s | Bytes transferidos por segundo |

> [!note] Base 10 vs Base 2
> Em I/O, 1 GB = 10⁹ bytes (base 10). Em memória, 1 GB = 2³⁰ bytes (base 2). Conversão introduce erro pequeno mas perceptível.

## Throughput vs. Latência

- **Latência baixa**: servir cada requisição o mais rápido possível → minimiza tempo de resposta individual
- **Throughput alto**: agrupar requisições relacionadas (ex: disco, acessos próximos) → maximiza ops/s mas aumenta variação de latência

Tradeoff inevitável: benchmarks muitas vezes limitam latência máxima aceita, tornando certas otimizações de throughput inviáveis.

## Benchmarks de IO

### Transaction Processing (TP)
Aplicações TP têm:
1. **Requisito de tempo de resposta** — cada transação deve completar dentro de um limite
2. **Métrica de throughput** — transações por segundo (TPS)

Organizações (TPC — Transaction Processing Performance Council) definem benchmarks padrão que:
- Concentram-se em throughput (não só latência)
- Exigem que datasets escalem com o número de usuários
- São definidos por organizações, não indivíduos

## Metodologia do Elo Mais Fraco (Weakest Link)

Para projetar um sistema de I/O que atenda a uma largura de banda alvo:

1. **Encontrar o gargalo** — componente com menor capacidade no caminho de I/O (pode ser CPU, barramento, controlador, ou disco)
2. **Configurar esse componente** para sustentar a banda exigida
3. **Dimensionar o restante** do sistema para suportar essa banda

### Exemplo

Sistema:
- CPU: 3 × 10⁹ inst/s; 100.000 inst por operação de I/O → **30.000 I/Os/s** (teto da CPU)
- Barramento: 1000 MB/s com transferências de 64 KB → **15.625 I/Os/s** (teto do barramento)
- Disco (10.000 RPM, seek = 6 ms): T\_IO = 6,9 ms → **146 I/Os/s por disco**

Gargalo = disco. Para 10.000 I/Os/s: 10.000 / 146 ≈ **69 discos** necessários.

## Tempo de IO no Disco

$$T_\text{IO\_disco} = T_\text{seek} + T_\text{rotacional} + T_\text{transferência}$$

Exemplo (seek=6 ms, 15.000 RPM, transfer=75 MB/s, bloco=64 KB):

$$T = 6 + 2{,}0 + \frac{64\ \text{KB}}{75\ \text{MB/s}} \approx 6{,}9\ \text{ms}$$

Capacidade por disco: 1000 ms / 6,9 ms ≈ **146 I/Os/s**

## Relações (SPO)

- Desempenho de IO → depende de → gargalo no caminho de I/O
- I/O rate → mede → operações por segundo
- Data rate → mede → bytes por segundo
- Latência × Throughput → têm → tradeoff fundamental
- [[Teoria de Filas para IO]] → modela → tempo de espera sob carga
- [[Disco Magnético]] → é o → gargalo típico em sistemas de armazenamento
- TPC → define → benchmarks de transaction processing

## Ver também

- [[Disco Magnético]]
- [[RAID]]
- [[Teoria de Filas para IO]]
- [[Barramento]]
- [[DMA]]
- [[Confiabilidade e Disponibilidade]]
- [[Benchmarks de Desempenho]]
