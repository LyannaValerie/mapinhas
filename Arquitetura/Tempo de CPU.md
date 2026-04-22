---
tags:
  - arquitetura/desempenho
  - arquitetura/processador
aliases:
  - CPU Time
  - Equação de Desempenho
---

# Tempo de CPU

Medida fundamental de desempenho de um programa em dado processador.

$$
\text{CPU time} = \text{Instruction count} \times \text{CPI} \times \text{Clock cycle time}
$$

Equivalente:

$$
\text{CPU time} = \frac{\text{Instruction count} \times \text{CPI}}{\text{Clock rate}}
$$

## Componentes

| Fator | Controlado por |
|---|---|
| Clock cycle time | Tecnologia de hardware, organização |
| CPI (ver [[Desempenho do Processador]]) | Organização, ISA |
| Instruction count | ISA, compilador |

> [!note] Independência relativa
> Os três fatores são interdependentes na prática, mas muitas otimizações afetam principalmente um deles.

## CPI Composto

Quando há múltiplos tipos de instrução com CPIs distintos:

$$
\text{CPI} = \sum_{i=1}^{n} \text{CPI}_i \times \frac{IC_i}{\text{Instruction count}}
$$

$\text{CPI}_i$ deve ser **medido** (inclui pipeline stalls, cache misses, etc.), não extraído de tabela.

## Desempenho Relativo

X é $n$ vezes mais rápido que Y:

$$
n = \frac{\text{Execution time}_Y}{\text{Execution time}_X} = \frac{\text{Performance}_X}{\text{Performance}_Y}
$$

## Tipos de Tempo

- **Wall-clock time / elapsed time**: latência total (I/O, OS overhead, esperas)
- **CPU time**: tempo de computação puro, excluindo I/O e outros processos

## Relações

- [[Lei de Amdahl]] — limite superior de speedup por otimização parcial
- [[Benchmarks de Desempenho]] — como medir tempo de execução real
- [[Gerenciamento de Consumo Elétrico]] — trade-off entre desempenho e consumo
