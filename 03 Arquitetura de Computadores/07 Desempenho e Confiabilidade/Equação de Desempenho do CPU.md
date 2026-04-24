---
title: Equação de Desempenho do CPU
aliases:
  - CPU performance equation
  - equação de desempenho
  - CPU time formula
  - tempo de CPU
  - CPI
  - instruction count
tags:
  - arquitetura/desempenho
  - arquitetura/quantitativo
source: "P&H CA:AQA Cap. 1 §1.8–1.9"
date: 2026-04-23
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Equação de Desempenho do CPU

Ferramenta quantitativa central para medir e comparar desempenho de processadores. Decompõe o tempo de execução em três fatores ortogonais.

## A Equação

$$\text{CPU time} = \text{IC} \times \text{CPI} \times \text{Clock cycle time} = \frac{\text{IC} \times \text{CPI}}{\text{Clock rate}}$$

| Fator | Definição | Quem controla |
|---|---|---|
| **IC** (Instruction Count) | Número de instruções executadas | Algoritmo + compilador + ISA |
| **CPI** (Cycles Per Instruction) | Média de ciclos por instrução | Microarquitetura + memória |
| **Clock cycle time** | Duração de um ciclo (= 1 / clock rate) | Tecnologia + microarquitetura |

> [!warning] Regra fundamental
> Nenhum dos três fatores isolado é métrica válida de desempenho. Clock rate alto pode ser compensado por CPI alto; IC pequeno pode exigir CPI maior. Apenas o **tempo de execução** é a métrica correta.

## CPI Ponderado

Quando diferentes classes de instrução têm CPI distinto:

$$\text{CPI} = \sum_{i=1}^{n} \text{CPI}_i \times \frac{\text{IC}_i}{\text{IC}}$$

onde $\text{IC}_i / \text{IC}$ é a frequência relativa da classe $i$.

### Exemplo (P&H §1.9)

| Classe | Freq | CPI |
|---|---|---|
| Outras | 75% | 1,33 |
| FP ops | 25% | 4,0 |
| FSQRT (subconjunto de FP) | 2% | 20 |

$$\text{CPI}_\text{original} = (1{,}33 \times 0{,}75) + (4{,}0 \times 0{,}25) = 2{,}0$$

**Alternativa A**: reduzir CPI do FSQRT de 20 → 2:

$$\text{CPI}_\text{nova FSQRT} = 2{,}0 - 2\% \times (20 - 2) = 1{,}64$$

**Alternativa B**: reduzir CPI médio de todas as FP ops para 2,5:

$$\text{CPI}_\text{nova FP} = (1{,}33 \times 0{,}75) + (2{,}5 \times 0{,}25) = 1{,}625$$

Alternativa B dá speedup ligeiramente maior (1,23× vs 1,22×), pois age numa fração maior do tempo total.

## Comparação de Desempenho

$$n = \frac{\text{Execution time}_Y}{\text{Execution time}_X} = \frac{\text{Performance}_X}{\text{Performance}_Y}$$

"X é $n$ vezes mais rápido que Y."

Performance é o inverso do tempo de execução:

$$\text{Performance} = \frac{1}{\text{Execution time}}$$

## Response Time vs. Throughput

| Métrica | Definição | Contexto típico |
|---|---|---|
| **Response time** | Tempo entre início e fim de uma tarefa | Usuário individual |
| **Throughput** | Total de trabalho por unidade de tempo | Data center, servidor |

- Processador mais rápido → reduz response time e melhora throughput
- Mais processadores em paralelo → melhora throughput sem reduzir response time individual

## CPU Time vs. Elapsed Time

**CPU time** (o que a equação mede): tempo que o processador gasta executando o programa, excluindo espera por I/O ou por outros processos.

**Elapsed (wall-clock) time**: tempo total percebido pelo usuário. Inclui CPU time + I/O + OS overhead + outros processos.

> [!note]
> A equação acima mede **CPU time**, não elapsed time. Para benchmarks de desempenho sistêmico, elapsed time é mais relevante.

## MIPS — Métrica Problemática

$$\text{MIPS} = \frac{\text{IC}}{\text{Execution time} \times 10^6} = \frac{\text{Clock rate}}{\text{CPI} \times 10^6}$$

> [!danger] Falácia
> MIPS ignora o IC e assume que todas as instruções fazem o mesmo trabalho. Dois problemas:
> 1. MIPS varia entre programas no mesmo processador
> 2. MIPS não é comparável entre ISAs diferentes (uma instrução CISC pode equivaler a 3 RISC)
>
> Um compilador que gera menos instruções mais lentas pode ter MIPS menor mas tempo de execução menor.

## Trade-offs Comuns

| Técnica | Efeito em IC | Efeito em CPI | Efeito em Clock rate |
|---|---|---|---|
| ISA mais rica (CISC) | ↓ | ↑ | ↓ |
| Pipeline mais profundo | — | ↓ (ideal) / ↑ (hazards) | ↑ |
| Compilador otimizador | ↓ | — | — |
| Cache maior | — | ↓ (menos miss) | ↓ (ciclo mais longo) |
| Superescalar | — | ↓ | — |

> [!tip] Corolário de Amdahl
> "Make the common case fast": o ganho de uma melhoria é proporcional ao tempo que ela é usada. Otimizar o caso raro tem impacto limitado mesmo com aceleração dramática.

## Relações

- [[Lei de Amdahl]] — speedup de melhorias parciais; complementar à equação de desempenho
- [[Benchmarks de Desempenho]] — SPEC mede CPU time de programas reais
- [[Previsão de Desvio]] — reduz CPI de instruções de branch
- [[Escalonamento Dinâmico]] — reduz CPI via out-of-order execution
- [[Hierarquia de Memória]] — cache misses aumentam CPI efetivo
- [[Gerenciamento de Consumo Elétrico]] — DVFS altera clock rate dinamicamente
