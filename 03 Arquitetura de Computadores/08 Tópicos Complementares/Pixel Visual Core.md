---
title: Pixel Visual Core
aliases:
  - Pixel Visual Core
  - IPU
  - image processing unit
  - pISA
tags:
  - computação/arquitetura
  - hardware/aceleradores
  - hardware/mobile
date: 2026-04-22
source: "Patterson & Hennessy — Computer Architecture: A Quantitative Approach, Cap. 7.7"
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Pixel Visual Core

**Pixel Visual Core** — [[Arquitetura de Domínio Específico — DSA|DSA]] do Google para processamento de imagem em **dispositivos móveis pessoais (PMD)**. Exemplifica a classe de **Image Processing Units (IPUs)**.

→ Produto: Google Pixel 2 (câmera)  
→ Design: multicore, 2–16 núcleos  
→ Configuração: chip próprio ou parte de SOC  
→ Linguagem de domínio: **Halide** → vISA → pISA

---

## IPU vs GPU

| Aspecto | GPU | IPU |
|---|---|---|
| Operação | **Gera** imagem de saída | **Analisa e modifica** imagem de entrada |
| Domínio | Gráficos, computação geral | Visão computacional, câmera |
| Dependência | Standalone para gráficos | Convive com CPU/GPU para não-visão |
| Paralelismo | SIMD 1D massivo + multithreading | SIMD 2D (array de PEs) |

IPU "resolve o problema inverso do GPU."

---

## Predecessores: ISPs

**Image Signal Processors (ISPs)** — aceleradores hardwired em PMDs para:
- Correção de lente
- Redução de ruído
- HDR (High Dynamic Range)
- Balanço de branco

ISPs são fixos em hardware. IPUs como Pixel Visual Core são programáveis via Halide.

---

## Terminologia

| Termo | Sigla | Descrição |
|---|---|---|
| Core | — | Processador; Pixel Visual Core suporta 2–16 |
| Physical ISA | pISA | ISA executado pelo hardware |
| Virtual ISA | vISA | ISA de alto nível gerado pelo compilador Halide |
| Sheet Generator | SHG | Faz acesso a memória de blocos 1×1 até 31×31 pixels |
| Processing Element Array | — | Array 16×16 de PEs + halo; opera multiply-add 16-bit |
| Line Buffer | LB | Memória para dados de imagem linha a linha |
| Network on Chip | NOC | Rede que conecta os núcleos |

---

## Arquitetura do Núcleo

### Array de Processing Elements

- **16 × 16 PEs** + **halo de 2 elementos** → suporta stencils de até **5 × 5**
- Halo: PEs extras nas bordas que mantêm vizinhos para cálculos de stencil sem acesso à memória externa
- Cada PE contém: Vector Lane + memória local
- **Rede de shifting 2D**: PEs podem transferir dados em massa para vizinhos nas 4 direções — captura relacionamento espacial 2D entre pixels

### pISA — Instrução VLIW de 119 bits

- Instrução de 119 bits (muito larga) = VLIW
- Mas kernels Halide são **curtos**: 200–600 instruções
- Memória de instrução: apenas **2048 pISA** = 28,5 KiB (IPU não precisa executar programas grandes)
- Scalar Lane: despacha instruções para o Sheet Generator com interface DMA-like

---

## Fluxo de Programação

```
Halide (Python/C++) — linguagem de domínio para processamento de imagem
       ↓ compilador Halide
   vISA — Virtual ISA (instrução de alto nível, agnóstica ao hardware)
       ↓ tradução para hardware
   pISA — Physical ISA (119-bit VLIW executado pelos PEs)
```

Exemplo: filtro blur em x e y usando aritmética 16-bit

```vISA
// blur em x
t1 ← input[x+1, y]
t2 ← input[x, y]
t4 ← t2 × 2
t5 ← t1 + t4 + input[x-1, y]   // média ponderada
```

---

## Como Pixel Visual Core Segue as 5 Diretrizes DSA

1. **Memórias dedicadas**: line buffers para dados de imagem; SHG para acesso eficiente a blocos
2. **Mais unidades aritméticas**: 16×16 = 256 PEs por núcleo, com shifting 2D
3. **Paralelismo do domínio**: SIMD 2D (vs SIMD 1D de CPUs) — natural para imagens 2D
4. **Tipo de dado**: aritmética 16-bit para pixels (vs FP32 de CPU)
5. **Linguagem de domínio**: Halide — projetada para visão computacional

---

## Desempenho (Contexto Cap. 7.9)

Pixel Visual Core: ~metade do TPU em performance por watt, ~25× GPU, ~100× CPU para workloads de visão.

---

## Relações (SPO)

- Pixel Visual Core → é → IPU para PMD (processamento de imagem, câmera)
- IPU → resolve → problema inverso do GPU (analisar vs gerar imagem)
- Array 16×16 PE + halo → suporta → stencils de até 5×5
- Halide → compila para → vISA → pISA (119-bit VLIW)
- SHG → acessa → blocos de pixels via DMA-like interface
- Rede shifting 2D → permite → transferência de dados entre PEs vizinhos em 4 direções
- ISP → é → predecessor hardwired (fixo) do IPU

---

## Ver também

- [[Arquitetura de Domínio Específico — DSA]] — contexto geral; diretrizes que guiaram o design
- [[Redes Neurais Profundas — DNN]] — stencils de CNN são computação central do IPU
- [[GPU — Arquitetura SIMD]] — comparativo (GPU gera imagem; IPU analisa)
- [[TPU — Tensor Processing Unit]] — prima no data center (Google, mesma família DSA)
- [[Instruções SIMD]] — relação com SIMD 2D do IPU
- [[FPGA]] — alternativa reconfigurável para visão
