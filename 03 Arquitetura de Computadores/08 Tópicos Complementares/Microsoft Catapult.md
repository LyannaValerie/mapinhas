---
title: Microsoft Catapult
aliases:
  - Catapult
  - Microsoft Catapult FPGA
tags:
  - computação/arquitetura
  - hardware/aceleradores
date: 2026-04-22
source: "Patterson & Hennessy — Computer Architecture: A Quantitative Approach, Cap. 7.5"
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Microsoft Catapult

**Catapult** — [[Arquitetura de Domínio Específico — DSA|DSA]] flexível da Microsoft baseado em [[FPGA]] (Intel Stratix V) para data center. Diferente do [[TPU — Tensor Processing Unit|TPU]] (ASIC fixo), o Catapult é reconfigurável: a mesma placa de hardware pode ser reprogramada para diferentes aplicações.

→ Motivação: qualquer solução de data center deve ser economicamente viável **sem** risco único de objetivo  
→ Implantação: metade dos servidores Microsoft conectados por rede de FPGAs  
→ Aplicações: ranking de busca Bing, aceleração de CNN, rede de armazenamento

---

## Arquitetura: Shell e Role

O código RTL do Catapult é dividido em duas partes:

| Parte | Nome | Conteúdo | Quem gerencia |
|---|---|---|---|
| Infraestrutura fixa | **Shell** | PCIe, DMA, I2C, JTAG, sensores de temperatura, LEDs, roteador inter-FPGA | Microsoft (não muda com aplicação) |
| Aplicação | **Role** | Lógica específica da aplicação sendo acelerada | Desenvolvedor |

- A **shell** mantém a placa operacional e conectada independente da role
- Trocar aplicação = recarregar apenas a **role** via novo bitstream
- Roteador inter-FPGA conecta servidores adjacentes em topologia de rede

---

## Acelerador CNN no Catapult

Microsoft desenvolveu acelerador CNN configurável como aplicação para Catapult:

- **Run-time configurable**: número de camadas, dimensões, precisão numérica — sem recompilação com ferramentas FPGA
- **Buffer eficiente**: minimiza acessos à memória via buffers otimizados para estruturas CNN
- **Array 2D de PEs (Processing Elements)**: escala até milhares de unidades

Fluxo:
```
Imagens → DRAM → multibank buffer (FPGA) → PEs (stencil computations) → feature maps de saída
Controller (top) → orquestra o fluxo
```

Suporta paralelismo **SIMD 2D** para CNN e **MISD** para a fase de ranking (Machine Scoring).

---

## Bing Search Ranking

Primeira aplicação implantada em produção — ranking de documentos de busca Bing:

**Pipeline de ranking:**
1. **Feature Extraction**: calcula features como frequência da frase de consulta no documento
2. **Free-Form Expressions**: calcula milhares de combinações de features do estágio anterior
3. **Machine-Learned Scoring**: avalia features via ML para produzir score float de relevância

> [!note] Fidelidade
> A implementação Catapult do ranking produz resultados **idênticos** ao software Bing equivalente — inclusive reproduzindo bugs conhecidos.

**Desempenho (95th percentile, latência ≤ bound normalizado):**
- Catapult: **1,95×** o throughput do servidor Intel puro
- Com constraint de latência, CPU e GPU têm throughput desperdiçado por não atenderem ao deadline

---

## Como Catapult Segue as 5 Diretrizes DSA

1. **Memórias dedicadas**: buffers eficientes de CNN dentro do FPGA minimizam DRAM accesses
2. **Mais unidades aritméticas**: array 2D de PEs (pode escalar a milhares)
3. **Paralelismo do domínio**: SIMD 2D para CNN; MISD para ranking
4. **Tipo de dado flexível**: suporta qualquer precisão — INT8 a FP32 — conforme necessário pela aplicação
5. **Linguagem de domínio**: Halide para visão; OpenCL/HDL para outras aplicações

---

## FPGA vs ASIC para Data Center

| Aspecto | FPGA (Catapult) | ASIC (TPU) |
|---|---|---|
| NRE | Baixo | Alto |
| Flexibilidade | Alta (reconfigurável) | Nenhuma |
| Eficiência energética | Menor | Maior |
| Tempo para produção | Dias | Meses/anos |
| Risco | Menor | Maior |

FPGA viabiliza aceleração quando volume ou diversidade de aplicações não justifica ASIC dedicado.

---

## Relações (SPO)

- Catapult → é → DSA baseado em FPGA para data center Microsoft
- Shell → gerencia → infraestrutura (PCIe, DMA, rede inter-FPGA)
- Role → implementa → lógica da aplicação específica
- Catapult → acelerou → ranking Bing com 1,95× throughput
- FPGA → permite → reconfiguração sem novo chip
- Catapult → aplica → SIMD 2D para CNN; MISD para ranking

---

## Ver também

- [[FPGA]] — tecnologia base (Intel Stratix V)
- [[Arquitetura de Domínio Específico — DSA]] — contexto geral
- [[Redes Neurais Profundas — DNN]] — CNN como aplicação no Catapult
- [[TPU — Tensor Processing Unit]] — ASIC alternativo (Google)
- [[Intel Crest]] — acelerador para treinamento
- [[WSC — Warehouse Scale Computer]] — data center onde Catapult é implantado
