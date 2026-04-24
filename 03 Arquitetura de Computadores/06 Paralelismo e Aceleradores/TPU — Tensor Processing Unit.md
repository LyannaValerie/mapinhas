---
title: TPU — Tensor Processing Unit
aliases:
  - TPU
  - Tensor Processing Unit
  - Google TPU
tags:
  - computação/arquitetura
  - hardware/aceleradores
date: 2026-04-22
source: "Patterson & Henneshy — Computer Architecture: A Quantitative Approach, Cap. 7.4"
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# TPU — Tensor Processing Unit

**Tensor Processing Unit** — ASIC customizado do Google para **inferência** de [[Redes Neurais Profundas — DNN|DNN]] no data center. Projetado em ~15 meses para ser inserido em servidores existentes via slot SATA/PCIe.

→ Tipo: [[Arquitetura de Domínio Específico — DSA]] custom (ASIC)  
→ Foco: inferência (não treinamento — ver [[Intel Crest]])  
→ Hardware coprocessador: host CPU invoca TPU via driver

---

## Componentes Principais

| Componente | Tamanho | Função |
|---|---|---|
| **Matrix Multiply Unit (MMU)** | 256 × 256 × 8-bit MACs = 65.536 ALUs | Array sistólico 2D; operação central |
| **Unified Buffer (UB)** | 24 MiB on-chip | Guarda ativações (feature maps, vetores MLP/LSTM) |
| **Accumulators** | 4.096 × 256 × 32-bit = 4 MiB | Coleta saída do MMU; entrada da Activation Unit |
| **Activation Unit** | — | Aplica ReLU, sigmoid, tanh, max pool, avg pool |
| **Weight Memory** | 8 MiB DRAM externa | Armazena pesos para o MMU; carregados via Weight FIFO |

**Área do die (floorplan):**
- Buffers de dados (UB + accumulators): 37%
- Unidades de computação (MMU + activation): 30%
- I/O (PCIe, DDR3 ports): 10%
- Controle: **2%** — contraste com CPU/GPU onde controle é muito maior

---

## Array Sistólico — MMU

**Systolic array**: array de elementos de processamento que, em lockstep, recebem dados de vizinhos upstream, computam resultados parciais e passam entradas + resultados a vizinhos downstream.

```
Dados (pesos) fluem de cima para baixo
Ativações fluem da esquerda para direita
Resultados parciais acumulam à medida que descem
```

- 256 × 256 unidades de multiply-add de 8-bit
- Internamente pipeline + organização sistólica
- Acumula em 32-bit (Accumulators)
- Pesos: INT8; ativações: INT8; acumulações: INT32

**Por que sistólico?** Elimina necessidade de buscar dados da memória a cada passo — dados fluem entre PEs. Reduz movimento de dados, que é o gargalo principal.

---

## Software Stack

```
TensorFlow (Python) → Compilador → API (roda em GPU ou TPU)
                                       ↓
                               User Space Driver
                               (muda frequentemente)
                               - Configura execução TPU
                               - Reformata dados para ordem TPU
                               - Traduz API calls → instruções TPU → binary
                                       ↓
                               Kernel Driver
                               (estável, mínimo)
                               - Gerenciamento de memória
                               - Interrupções
```

- Primeiro run: compila modelo, armazena em cache o programa e os pesos na Weight Memory
- Runs seguintes: velocidade máxima
- TPU executa a maioria dos modelos do input ao output sem retornar à CPU

---

## Como TPU Segue as 5 Diretrizes DSA

1. **Memórias dedicadas**: UB (24 MiB para ativações), Accumulators (4 MiB), Weight FIFO. Dados não ficam em múltiplas cópias de cache inclusiva.
2. **Mais unidades aritméticas**: 65.536 ALUs 8-bit; ~250× mais que CPU; ~25× mais que GPU; com metade do tamanho e potência da CPU.
3. **Paralelismo simples do domínio**: SIMD 2D sistólico. CPU usa multiprocessamento + OoO + SIMD 1D. GPU usa multithreading + SIMD 1D.
4. **Dados menores**: opera primariamente em INT8; CPU e GPU suportam também FP64.
5. **Linguagem de domínio**: TensorFlow; GPU usa CUDA/OpenCL.

---

## Análise de Desempenho (Roofline)

Gargalo principal: **bandwidth de memória** (Weight Memory), não clock nem tamanho do MMU.

| Parâmetro escalado | Efeito médio (0.25× a 4×) |
|---|---|
| **Bandwidth de memória (4×)** | Performance melhora **3×** |
| Clock rate (4×) | Pouca melhora |
| Dimensão do MMU (256→512) | Leve **degradação** (fragmentação interna 2D) |

> [!note] Por que maior MMU piora desempenho?
> Análogo à fragmentação interna de páginas grandes, mas em duas dimensões. Ex: LSTM1 usa matriz 600×600 — em MMU 256×256 cabe bem; em 512×512 há wasted capacity.

**Redesign hipotético**: trocar Weight Memory DDR3 → GDDR5 (mesma tecnologia do K80) → 3,2× melhora de desempenho. Aumentar clock sozinho: irrelevante.

---

## Comparação vs CPU e GPU (Cap. 7.9)

| Plataforma | IPS relativo | Performance/Watt relativo |
|---|---|---|
| Intel Haswell CPU | 1× | 1× |
| NVIDIA K80 GPU | ~1,9× | ~2,9× |
| Google TPU | **29,2×** | **~83×** |

- TPU 15,3× mais rápido que GPU
- Power: TPU ~40W vs CPU ~145W vs GPU ~150W
- TPU focado em 99th percentile de latência → sem microarquitetura que melhora caso médio mas piora cauda

**Roofline por plataforma:**

| Plataforma | Ridge point (MAC ops/byte) |
|---|---|
| Intel Haswell CPU | 13 |
| NVIDIA K80 GPU | 9 |
| Google TPU | ~1.350 |

TPU tem ridge point altíssimo → computa muito mais por byte buscado → adequado para workloads de alta intensidade operacional como DNN.

---

## Relações (SPO)

- TPU → executa → inferência de DNN como coprocessador
- MMU → implementa → array sistólico 256×256 de multiply-add 8-bit
- Unified Buffer → armazena → ativações (feature maps, vetores)
- Weight Memory → alimenta → MMU via Weight FIFO
- TensorFlow → compila para → instruções TPU
- Bandwidth de memória → é → gargalo principal do TPU (não clock, não tamanho de MMU)
- TPU → supera → K80 GPU em 15,3× para inferência DNN

---

## Ver também

- [[Arquitetura de Domínio Específico — DSA]] — diretrizes que guiaram o design do TPU
- [[Redes Neurais Profundas — DNN]] — domínio-alvo
- [[Modelo Roofline]] — análise de gargalo; ridge point TPU em ~1.350 MAC ops/byte
- [[GPU — Arquitetura SIMD]] — plataforma alternativa (K80); ridge point em 9
- [[Intel Crest]] — DSA para treinamento (contra-parte do TPU)
- [[Microsoft Catapult]] — alternativa FPGA flexível
- [[WSC — Warehouse Scale Computer]] — contexto de data center onde o TPU opera
