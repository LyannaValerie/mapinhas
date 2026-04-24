---
title: Modelo Roofline
tags:
  - hardware
  - arquitetura
  - DLP
  - desempenho
aliases:
  - Roofline model
  - roofline
  - intensidade aritmética
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Modelo Roofline

Modelo visual de desempenho (Williams et al., 2009) que relaciona **floating-point performance**, **bandwidth de memória** e **intensidade aritmética** para identificar o gargalo de um kernel.

## Intensidade Aritmética (AI)

$$AI = \frac{\text{FLOPs executados}}{\text{bytes transferidos para/da DRAM}}$$

Unidade: FLOP/byte.

Representa quanta computação se faz por byte de memória acessado. Exemplos típicos:

| Kernel | AI | Regime |
|---|---|---|
| DAXPY (`a*X + Y`) | ~0.25 FLOP/byte | Limitado por memória |
| FFT (Spectral methods) | O(log N) | Intermediário |
| SpMV (sparse matrix-vector) | ~0.17 FLOP/byte | Muito limitado por memória |
| Stencils / PDEs (structured grids) | baixa–média | Memória |
| Dense matrix (BLAS-3: DGEMM) | O(N) | Limitado por compute |
| N-body (particle methods) | alta | Compute |

## O "telhado" (roofline)

Desempenho atingível = mínimo entre dois limites:

$$\text{Perf} = \min\left(\text{Peak FLOPs/s},\;\; \text{Peak BW} \times AI\right)$$

Gráfico log-log (Y = GFLOPS/s, X = FLOP/byte):

```
GFLOPS/s
   │
256│  ─────────────────────── Peak Compute (horizontal)
   │                         /
   │                        /  ← diagonal: BW × AI
   │                       /
   │──────────────────────/─── ridge point
   │                     /
   │                    /
   │                   /
   └──────────────────────────── AI (FLOP/byte)
        ↑ memory-bound │ compute-bound ↑
```

- **Diagonal (BW × AI)**: limite de bandwidth — abaixo do ridge point
- **Horizontal (Peak FLOPs)**: limite de compute — acima do ridge point
- **Ridge point**: AI mínima para atingir peak compute = `Peak FLOPs / Peak BW`

## Exemplos reais (P&H Fig. 4.11)

Eixos: Y de 2–256 GFLOPS/s, X de 1/8–16 FLOP/byte (log-log).

### NEC SX-9 (processador vetorial)
- Peak BW (Stream benchmark): **102.4 GB/s**
- DAXPY (AI ≈ 0.25): ~3.2 GFLOPS/s → fortemente memory-bound
- Kernels com AI > ridge point atingem ~100+ GFLOPS/s

### Intel Core i7 920 (multicore)
- Peak BW: ~17 GB/s
- Ridge point mais à direita → mais kernels ficam memory-bound

## Como usar

1. Calcular AI do kernel (FLOPs teóricos / bytes movidos — ignorar caches)
2. Medir peak BW com **Stream benchmark** (bandwidth entregue ao processador, não na DRAM)
3. Plotar ponto (AI, desempenho medido) no gráfico
4. Se ponto está na diagonal → **memory-bound** → otimizar localidade / bandwidth
5. Se ponto está próximo do teto horizontal → **compute-bound** → otimizar FLOPs / ILP / SIMD

## Roofline para Aceleradores DNN (P&H Cap. 7.9)

Aplicado a CPU, GPU e TPU com workloads de inferência DNN. Eixos em MAC ops/byte (intensidade operacional de multiply-accumulate) e TeraOps/s.

**Ridge points (MAC ops/byte):**

| Plataforma | Ridge point | Regime da maioria dos DNNs |
|---|---|---|
| Intel Haswell CPU | **13** MAC ops/byte | memory-bound |
| NVIDIA K80 GPU | **9** MAC ops/byte | memory-bound |
| Google TPU | **~1.350** MAC ops/byte | compute-bound para muitos workloads |

TPU tem ridge point ~100× mais alto que CPU/GPU porque o array sistólico reusa pesos intensamente → muito mais operações por byte buscado da Weight Memory.

**Workloads DNN e regime:**

| Workload | Intensidade op. | CPU | GPU | TPU |
|---|---|---|---|---|
| MLP (fully connected) | baixa | memory-bound | memory-bound | memory-bound |
| LSTM | baixa-média | memory-bound | memory-bound | memory-bound |
| CNN | alta | compute-bound | compute-bound | compute-bound |

> [!note] Implicação para o TPU
> Bottleneck do TPU é bandwidth da Weight Memory (DRAM), não clock nem tamanho do MMU. Trocar DDR3 → GDDR5 daria 3,2× mais desempenho; aumentar clock: irrelevante.

**Performance com latência de 7ms (99th percentile):**

| Plataforma | IPS relativo | Performance/Watt relativo |
|---|---|---|
| Haswell CPU | 1× | 1× |
| K80 GPU | ~1,9× | ~2,9× |
| Google TPU | **29,2×** | **~83×** |

TPU é 15,3× mais rápido que K80 GPU. CPUs e GPUs têm throughput potencialmente maior, mas desperdiçado quando não atendem ao deadline de latência.

## Limitações

- Ignora: start-up time vetorial, overhead de loop, latência de TLB, contentão de banco
- AI assume que todos os dados vêm da DRAM (não de cache) — conservador
- Útil para comparação relativa entre arquiteturas SIMD, não como medida absoluta

## Relações (SPO)

- Modelo Roofline → expõe → gargalo compute vs. memória de um kernel
- Intensidade aritmética → determina → qual limite domina (compute ou bandwidth)
- Stream benchmark → mede → peak bandwidth entregue (não pinos da DRAM)
- Ridge point → define → limiar entre compute-bound e memory-bound
- DAXPY → é → referência de kernel memory-bound (AI ≈ 0.25 FLOP/byte)
- TPU → tem → ridge point ~1.350 MAC ops/byte (vs 9–13 de CPU/GPU)
- MLPs e LSTMs → são → memory-bound em todas as plataformas
- CNNs → são → compute-bound acima do ridge point

## Ver também

- [[Arquitetura Vetorial]] — NEC SX-9 como exemplo de roofline
- [[GPU — Arquitetura SIMD]] — roofline se aplica a GPUs para guiar otimização CUDA
- [[TPU — Tensor Processing Unit]] — ridge point ~1.350; gargalo em bandwidth
- [[Redes Neurais Profundas — DNN]] — MLP/LSTM memory-bound; CNN compute-bound
- [[Arquitetura de Domínio Específico — DSA]] — contexto das comparações Cap. 7
- [[Benchmarks de Desempenho]] — Stream como benchmark de bandwidth
- [[Hierarquia de Memória]] — bandwidth de DRAM vs. entregue ao processador
- [[Concorrência e Paralelismo]] — DLP e as três variações SIMD do Cap. 4
