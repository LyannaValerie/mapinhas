---
title: Arquitetura de Domínio Específico — DSA
aliases:
  - DSA
  - domain-specific architecture
  - arquitetura específica de domínio
  - acelerador de domínio
tags:
  - computação/arquitetura
  - hardware/aceleradores
date: 2026-04-22
source: "Patterson & Hennessy — Computer Architecture: A Quantitative Approach, Cap. 7"
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Arquitetura de Domínio Específico — DSA

**Domain-Specific Architecture** — processador de propósito especial projetado para um domínio particular. Depende de outros processadores (CPU, GPU) para processamento fora do domínio.

→ Motivação: fim da [[Lei de Moore]] e do escalonamento de Dennard → transistores extras não convertem em desempenho geral sem custo energético proibitivo  
→ Alternativa a chip customizado: [[FPGA]] (menor NRE, menor desempenho)  
→ Domínio principal desta geração: [[Redes Neurais Profundas — DNN]]

---

## Por Que DSAs Agora

Inovações das décadas anteriores (caches multi-nível, execução fora de ordem, SIMD, multithreading) maximizaram desempenho de código geral. Mas:

- Cache dois-vias usa 2,5× mais energia que scratchpad equivalente controlado por software
- Aplicações com padrões de acesso previsíveis ou grandes datasets (vídeo) têm pouco reuso → multilevel caches desperdiçam área e energia
- DSAs reciclam esses recursos para eficiência de silício **e** eficiência energética (hoje, a mais importante)

---

## As 5 Diretrizes para DSAs

Derivadas empiricamente pelos projetos Google TPU, Microsoft Catapult, Intel Crest e Pixel Visual Core:

| # | Diretriz | Exemplo nos DSAs |
|---|---|---|
| 1 | **Usar memórias dedicadas** para minimizar distância de movimento de dados | Unified Buffer (TPU), line buffers (Pixel Visual Core) |
| 2 | **Investir recursos economizados** (ao dropar microarquitetura avançada) em mais unidades aritméticas ou memórias maiores | TPU: 65.536 ALUs 8-bit; 28 MiB on-chip vs ~10 MiB em CPU |
| 3 | **Evitar otimizações de propósito geral** — branch prediction, execução OoO, caches; usar forma mais simples de paralelismo que basta ao domínio | TPU usa SIMD 2D sistólico; GPU usa SIMD 1D + multithreading |
| 4 | **Reduzir tamanho e tipo de dado** ao mínimo necessário | TPU opera em inteiros 8-bit; acumula em 32-bit; CPU/GPU suportam float64 |
| 5 | **Usar linguagem de programação de domínio** para portar código ao DSA | TPU: TensorFlow; Pixel Visual Core: Halide; GPU: CUDA/OpenCL |

**Efeitos bônus** dessas diretrizes:
- Designs mais simples → menor custo de NRE
- Melhor atendimento ao percentil 99 de latência (sem variações temporais de desempenho das otimizações tradicionais)

---

## Desafios

- **Escopo**: DSAs atendem kernels compute-intensivos, não programas inteiros
- **NRE**: custo de chip customizado amortizado pelo número de chips fabricados; sem volume, não faz sentido econômico
- **Software**: C++ e compiladores tradicionais raramente são o veículo certo para um DSA
- **Volume menor**: alternativa é [[FPGA]] — menor NRE, menor eficiência vs chip customizado
- **ISA do host**: decisão crítica para integração em SOC → RISC-V como opção aberta livre de licença

---

## Comparação: DSA vs GPU vs CPU

| Aspecto | CPU | GPU | DSA (ex: TPU) |
|---|---|---|---|
| Paralelismo | Multiprocessamento + OoO + SIMD 1D | Multithreading + SIMD 1D massivo | SIMD 2D sistólico |
| Controle | 15+ estágios pipeline, branch pred, OoO | Menor controle | ~2% do die (controle mínimo) |
| Dados | INT8→FP64 | INT8→FP64 | INT8, acumulação INT32 |
| Memória | Hierarquia cache inclusiva | Cache L2 unificada | Scratchpads dedicados |
| Programação | Qualquer coisa | CUDA, OpenCL | TensorFlow, Halide, etc. |

---

## Relações (SPO)

- DSA → especializa → hardware para domínio específico (ex: DNNs, visão)
- DSA → depende de → CPU/GPU para processamento fora do domínio
- DSA → substitui → caches por → scratchpads controlados por software
- FPGA → viabiliza → DSA de menor volume com NRE menor
- RISC-V → permite → integração SOC sem licença proprietária
- NRE → amortiza → sobre número de chips fabricados

---

## Ver também

- [[Redes Neurais Profundas — DNN]] — domínio-alvo desta geração de DSAs
- [[TPU — Tensor Processing Unit]] — DSA customizado Google para inferência
- [[Microsoft Catapult]] — DSA baseado em FPGA (Microsoft)
- [[Intel Crest]] — DSA para treinamento (Intel)
- [[Pixel Visual Core]] — DSA para processamento de imagem em PMDs
- [[FPGA]] — alternativa reconfigurável para volumes menores
- [[Lei de Moore]] — motivação para especialização
- [[GPU — Arquitetura SIMD]] — comparativo
- [[Modelo Roofline]] — ferramenta para analisar gargalo compute vs. memória
