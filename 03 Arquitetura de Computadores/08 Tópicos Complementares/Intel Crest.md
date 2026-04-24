---
title: Intel Crest
aliases:
  - Crest
  - Lake Crest
  - Knights Crest
  - Intel Nervana
tags:
  - computação/arquitetura
  - hardware/aceleradores
date: 2026-04-22
source: "Patterson & Hennessy — Computer Architecture: A Quantitative Approach, Cap. 7.6"
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Intel Crest

**Intel Crest** — [[Arquitetura de Domínio Específico — DSA|DSA]] da Intel para **treinamento** de [[Redes Neurais Profundas — DNN|DNN]] no data center. Foco oposto ao [[TPU — Tensor Processing Unit|TPU]] (inferência).

→ Origem: Intel adquiriu Nervana Systems  
→ Chip: **Lake Crest** — acelerador dedicado  
→ Variante híbrida: **Knights Crest** — chip combinando núcleos Xeon x86 + aceleradores Crest  
→ Precisão: maior que INT8 do TPU (FP16/FP32 necessário para backpropagation)

---

## Lake Crest — Especificações

| Parâmetro | Valor |
|---|---|
| Processo | TSMC 28 nm |
| Die size | ~600–700 mm² (quase reticle completo) |
| Memória | 4 stacks × 8 GB HBM2 = **32 GB** |
| Interface host | PCIe × 16 |
| Gestão | MGMT CPU |

**HBM2 (High Bandwidth Memory 2):**
- Empilhada no mesmo pacote via interposer
- Largura de banda muito maior que DDR/GDDR para o mesmo die
- Crítico para treinamento — gradientes exigem acesso intensivo a parâmetros

---

## Topologia Interna

```
PCIe Controller & DMA
        |
    [ICC — Inter-Cluster Controller]
   /    |     |    \
 PCL   PCL   PCL   PCL
 [PC]  [PC]  [PC]  [PC]   ← Processing Clusters
  |     |     |     |
HBM2  HBM2  HBM2  HBM2   ← 8 GB cada
```

- **Processing Clusters (PC)**: unidades de computação
- **ICL (Interconnect Link)**: rede que conecta clusters
- **ICC (Inter-Cluster Controller)**: roteamento entre clusters

---

## Treinamento vs Inferência

| Aspecto | Inferência (TPU) | Treinamento (Crest) |
|---|---|---|
| Fase | Produção | Desenvolvimento |
| Precisão | INT8 suficiente | FP16 ou FP32 necessário |
| Latência | Crítica (99th percentile) | Sem deadline duro |
| Dados | Um batch pequeno | Datasets enormes |
| Operação | Forward pass apenas | Forward + backpropagation |
| Memória | Pesos fixos | Pesos atualizados iterativamente |

> [!note] Por que FP16/FP32 para treinamento?
> Backpropagation requer gradientes precisos. Erros de arredondamento em INT8 impedem convergência. FP16 com acumulação em FP32 ("mixed precision") é o compromisso padrão.

---

## Knights Crest

Chip híbrido combinando:
- Núcleos **Xeon x86** (executa SO e código geral)
- Aceleradores **Crest** (executa kernels de treinamento DNN)

Integração estreita entre CPU e DSA no mesmo chip — elimina overhead de PCIe entre host e acelerador.

---

## Relações (SPO)

- Crest → foca em → treinamento de DNN (não inferência)
- Lake Crest → usa → HBM2 (32 GB, alta bandwidth) para gradientes
- Nervana Systems → foi adquirida por → Intel e tornou-se base do Crest
- Knights Crest → combina → núcleos Xeon + aceleradores Crest no mesmo chip
- Treinamento → exige → FP16/FP32 (vs INT8 do TPU para inferência)

---

## Ver também

- [[TPU — Tensor Processing Unit]] — DSA Google para inferência (contra-parte)
- [[Arquitetura de Domínio Específico — DSA]] — contexto geral
- [[Redes Neurais Profundas — DNN]] — domínio-alvo; distinção inferência vs treinamento
- [[GPU — Arquitetura SIMD]] — plataforma dominante para treinamento antes dos DSAs
- [[Microsoft Catapult]] — alternativa FPGA flexível
- [[WSC — Warehouse Scale Computer]] — contexto de data center
