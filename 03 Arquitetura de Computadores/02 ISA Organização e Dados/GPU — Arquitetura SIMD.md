---
title: GPU — Arquitetura SIMD
tags:
  - hardware
  - arquitetura
  - DLP
  - GPU
  - SIMD
aliases:
  - GPU
  - graphics processing unit
  - GPGPU
  - CUDA
  - PTX
  - warp
  - SIMD Processor
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# GPU — Arquitetura SIMD

GPU (Graphics Processing Unit) é um processador SIMD massivamente paralelo composto por um array de **SIMD Processors** multithreaded. Originalmente para gráficos; hoje uso geral (GPGPU) em HPC, ML, IA.

> [!info] SIMD vs. vetor
> GPUs são SIMD: muitas unidades funcionais paralelas (≠ pipelining profundo das [[Arquitetura Vetorial|arquiteturas vetoriais]]). Ambas exploram [[Concorrência e Paralelismo#SIMD — Instrução única, múltiplos dados|DLP]].

## Hierarquia de execução

```
GPU
└── N × SIMD Processors (multithreaded)
      └── Thread Blocks alocados pelo hardware
            └── SIMD Threads (Warps)
                  └── 32 CUDA Threads (1 por SIMD Lane)
```

| Nível | Alias NVidia | Descrição |
|---|---|---|
| **SIMD Processor** | Streaming Multiprocessor (SM) | Processador completo com PC próprio, scheduler, lanes |
| **Thread Block** | Block | Grupo de SIMD Threads que compartilham local memory |
| **SIMD Thread** | Warp | Thread de 32 instruções SIMD; unidade de escalonamento |
| **CUDA Thread** | Thread | 1 elemento escalar; mapeado a 1 SIMD Lane |

## SIMD Processor — estrutura interna

```
SIMD Processor
├── Instruction Cache
├── Warp Scheduler (+ scoreboard p/ 64 warps)
├── Instruction Registers
├── SIMD Lanes (×32)
│     ├── Registradores (1K × 32 bits por lane)
│     └── Pipeline de execução
├── L1 Cache
└── Local Memory (scratchpad — só dentro do Thread Block)
```

- **Warp Scheduler**: seleciona qual SIMD Thread executar a cada ciclo
- **Scoreboard**: rastreia até 64 SIMD Threads; identifica instruções prontas (latência de memória é variável — hits/misses em cache e TLB)
- **Local memory**: visível apenas às threads do mesmo Thread Block; ~= memória compartilhada por grupo de trabalho

## Thread Block e comunicação

- Threads do mesmo Thread Block → compartilham local memory → podem cooperar
- Threads de Thread Blocks diferentes → **sem** comunicação direta (só via memória global)
- Pascal (P100): máx 32 SIMD Threads por Thread Block

## CUDA Thread e endereçamento

CUDA expõe ao programador:

```c
// blockIdx: índice do Thread Block; threadIdx: índice do Thread dentro do Block
int i = blockIdx.x * blockDim.x + threadIdx.x;
Y[i] = a * X[i] + Y[i];  // cada thread processa 1 elemento
```

Hardware: mapeia 8192 iterações → 256 Thread Blocks × 32 SIMD Threads = 8192 CUDA Threads.

## PTX — Parallel Thread eXecution

ISA virtual da NVidia; compilado para hardware real **no momento do load** (permite portabilidade entre gerações):

| Registradores | Tipo |
|---|---|
| `R0, R1, ...` | 32 bits |
| `RD0, RD1, ...` | 64 bits (double) |
| `p0, p1, ...` | 1 bit (predicado) |

```ptx
// DAXPY em PTX (simplificado)
cvt.u64.u32  RD8, R0         ; calcula byte offset
ld.global.f64 RD0, [RD8+base_X]
ld.global.f64 RD1, [RD8+base_Y]
fma.rn.f64   RD2, RD0, a, RD1
st.global.f64 [RD8+base_Y], RD2
```

Toda instrução pode ser **predicada** por registrador de 1 bit → branches condicionais.

## Memory Coalescing

Para bandwidth eficiente:

- CUDA Threads adjacentes devem acessar endereços **próximos** → hardware agrupa em 1–2 transferências de bloco (32 palavras sequenciais)
- Caso contrário: N acessos individuais → N vezes mais latência
- GPU não tem lógica especial de stride como [[Arquitetura Vetorial]]; todos os loads são efetivamente **gathers**

## Desvio Condicional (Warp Divergence)

IF statements dentro de um warp causam **divergência**:

```c
if (X[i] > 0)   // alguns threads: true; outros: false
    Y[i] = X[i];
```

- GPU usa **máscara interna** (não visível ao software — diferente de [[Arquitetura Vetorial#Registradores de Máscara (Predicate Registers)|predicate registers]] explícitos do vetorial)
- Threads com máscara=0 ainda executam mas descartam resultado (mesmo custo)
- GFLOPS efetivo cai proporcionalmente à divergência
- Compilador/PTX aplica **if-conversion**: converte branch em execução predicada

## GPU vs. Arquitetura Vetorial — diferenças chave

| Aspecto | Arquitetura Vetorial | GPU |
|---|---|---|
| Unidades funcionais | Poucas, deep-pipelined | Muitas, paralelas |
| Máscara condicional | Predicate regs arquiteturais | Máscara interna (hardware) |
| Multithreading | Não | Sim — esconde latência via warp switching |
| Memória | Cache + stride/scatter hw | Coalescing; scratchpad por bloco |
| Programação | Compilador vetorizador | CUDA / OpenCL |

## Pascal P100 — especificações

| Parâmetro | Valor |
|---|---|
| SIMD Processors | 56 (60 blocos físicos, 4 spares para yield) |
| SIMD Lanes por SM | 32 |
| Memória | 4 portas HBM2; até 16 GB |
| Largura do barramento | 4096 bits |
| Transistores | 15.4 bilhões |
| Tecnologia de memória | HBM2 (High Bandwidth Memory 2) — stacked DRAM |

## Relações (SPO)

- GPU → implementa → DLP via array de SIMD Processors multithreaded
- Warp Scheduler → usa → scoreboard para gerenciar latências de memória variáveis
- Thread Block → define → escopo de comunicação via local memory
- Memory coalescing → converte → gathers individuais em transferências eficientes de bloco
- PTX → compilado em → ISA da GPU no load-time (portabilidade entre gerações)
- Warp divergence → reduz → GFLOPS efetivo via execução mascarada

## Ver também

- [[Arquitetura Vetorial]] — alternativa: pipelined deep, predicate registers arquiteturais
- [[Concorrência e Paralelismo]] — GPU como SIMD na taxonomia de Flynn; GPGPU
- [[Instruções SIMD]] — SIMD multimídia x86 (diferente de GPU e vetorial)
- [[Modelo Roofline]] — ferramenta para analisar gargalo compute vs. bandwidth em GPUs
- [[Multithreading no Chip]] — GPU usa multithreading massivo para esconder latência
- [[Hierarquia de Memória]] — HBM2, coalescing, TLB no scoreboard
- [[WSC — Warehouse Scale Computer]] — GPUs como aceleradores em datacenters
