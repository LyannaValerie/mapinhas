---
tags:
  - arquitetura/desempenho
  - arquitetura/medição
aliases:
  - SPEC
  - benchmark
  - SPECpower
  - medição de desempenho
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Benchmarks de Desempenho

Métodos padronizados para medir e comparar desempenho de sistemas de computação.

## Hierarquia de Representatividade

Do mais ao menos representativo:

1. **Aplicações reais** — gold standard (ex: Google Translate)
2. **Kernels** — trechos-chave de aplicações reais
3. **Toy programs** — programas de ~100 linhas (Quicksort, etc.) — problemáticos
4. **Benchmarks sintéticos** — mede características isoladas — problemáticos

> [!warning] Princípio
> Somente o tempo de execução de programas reais é medida confiável de desempenho. Alternativas levam sistematicamente a conclusões erradas no design de computadores.

## SPEC (Standard Performance Evaluation Corporation)

### SPEC CPU2017

- Benchmark padrão para desempenho de processador
- Programas reais de diversas cargas (científico, engenharia, etc.)
- Suítes: **SPECspeed** (tempo de execução individual) e **SPECrate** (throughput, múltiplas cópias em paralelo)
- SPECrate mede **paralelismo a nível de requisição** (RLP)

### Outros benchmarks SPEC ativos (2017)

- **SPECapc**: aplicações gráficas CAD (Creo, SolidWorks, Siemens NX)
- **ACCEL**: OpenCL/OpenACC em GPU + CPU
- **MPI2007**: aplicações MPI paralelas (TLP)
- **OMP2012**: OpenMP (TLP)

## SPECpower

- Benchmark de desempenho × consumo de energia para servidores
- Baseado em **SPECjbb** (Java server-side, simulando aplicações de negócio)
- Métrica: **ssj_ops/watt** (server-side Java operations per segundo por Watt)
- Exercita processador, memória, storage, rede e SO — não apenas CPU

## TPC (Transaction Processing Council)

- **TPC-C**: OLTP (Online Transaction Processing) — simula empresa de atacado
- Métricas: transações por minuto (**tpmC**) e custo por tpmC
- Inclui requisito de tempo de resposta (throughput medido sob restrição de latência)

## Benchmarks de Servidor

| Benchmark | Tipo de paralelismo medido |
|---|---|
| SPECrate | RLP (request-level) |
| SPECjbb / SPECpower | RLP + memória + SO |
| SPEC MPI / OMP | TLP (thread-level) |
| TPC-C/E | OLTP, RLP |

## Médias de Desempenho

Para comparar desempenho entre múltiplos programas:

**Média Aritmética (AM):**
$$AM = \frac{1}{n}\sum_{i=1}^{n} \text{Time}_i$$

**Média Aritmética Ponderada (WAM):**
$$WAM = \sum_{i=1}^{n} w_i \times \text{Time}_i$$

**Média Geométrica (GM):**
$$GM = \left(\prod_{i=1}^{n} \text{Time}_i\right)^{1/n}$$

> [!note] Por que GM para benchmarks
> GM é a única média que produz o mesmo ranking independente da máquina de referência usada para normalizar os tempos. SPEC usa GM para agregar resultados.

## Peak Performance vs. Observed Performance

> [!warning] Falácia
> "Peak performance representa desempenho observado."
>
> Definição válida de peak: "nível que o computador **garante não exceder**". Gap entre peak e real varia de 5% a 58% dependendo do benchmark e do programa.

## Relações

- [[Tempo de CPU]] — o que medir: wall-clock time vs CPU time
- [[Lei de Amdahl]] — speedup teórico vs. medido
- [[WSC — Warehouse Scale Computer]] — SPECpower relevante para datacenters
- [[Concorrência e Paralelismo]] — SPECrate/MPI medem tipos distintos de paralelismo
