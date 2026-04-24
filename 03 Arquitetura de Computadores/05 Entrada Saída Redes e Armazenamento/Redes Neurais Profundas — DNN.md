---
title: Redes Neurais Profundas — DNN
aliases:
  - DNN
  - deep neural network
  - rede neural profunda
  - MLP
  - CNN
  - LSTM
  - RNN
  - inferência
  - treinamento DNN
tags:
  - computação/arquitetura
  - hardware/aceleradores
  - IA/redes-neurais
date: 2026-04-22
source: "Patterson & Hennessy — Computer Architecture: A Quantitative Approach, Cap. 7.3"
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Redes Neurais Profundas — DNN

**Deep Neural Network** — sequência de camadas contendo neurônios artificiais. Cada neurônio aplica função não-linear ao produto de pesos × saídas da camada anterior.

→ Motivação para hardware especializado: operações de DNN mapeiam diretamente em multiply-add massivo → [[TPU — Tensor Processing Unit]], [[GPU — Arquitetura SIMD]]  
→ Plataforma de programação dominante: TensorFlow, PyTorch  
→ Contexto arquitetural: [[Arquitetura de Domínio Específico — DSA]]

---

## Terminologia Central

| Termo | Significado |
|---|---|
| **Pesos (weights/parameters)** | Valores aprendidos durante treinamento; aplicados às entradas na inferência |
| **Ativação** | Saída da função não-linear de um neurônio |
| **Inferência** | Fase de produção — aplica pesos treinados a novos dados; também chamada de *prediction* |
| **Treinamento** | Fase de desenvolvimento — aprende pesos a partir de dados; também chamada de *learning* |
| **Batch** | Conjunto de exemplos processados juntos para amortizar custo de busca dos pesos |
| **ReLU** | Função não-linear: f(x) = max(x, 0). Alternativas: sigmoid, tanh |
| **Intensidade operacional** | Operações / byte de peso buscado — determina se kernel é compute-bound ou memory-bound |

---

## Tipos de DNN

### MLP — MultiLayer Perceptron

- Cada nova camada é conjunto de funções não-lineares de **todas** as saídas da camada anterior × pesos
- Camadas chamadas **fully connected**
- Operação central: vector-matrix multiply
- Intensidade operacional: baixa-média (memory-bound em hardware)

```
Camada[i]: y = ReLU(W · x)
  W: matriz de pesos (dim[i] × dim[i-1])
  x: vetor de ativações da camada anterior
```

### CNN — Convolutional Neural Network

- Entradas: funções não-lineares de regiões **espacialmente próximas** da saída da camada anterior × pesos
- Operação: **stencil 3D** sobre feature maps de entrada → feature map de saída
- Cada feature map de saída tem conjunto único de pesos
- Intensidade operacional: maior que MLP (mais compute por peso buscado)
- Uso: reconhecimento de imagem, vídeo

```
Operações/camada = 2 × DimFM[i]² × NumFM[i] × NumFM[i-1] × DimSten[i]²
Pesos/camada     = NumFM[i] × NumFM[i-1] × DimSten[i]²
Ops/peso         = 2 × DimFM[i]²
```

**Exemplo:** camada com DimFM=14, DimSten=3, 64 feature maps entrada, 128 saída:
- 73.728 pesos, 28.9M operações, intensidade operacional = 392

### RNN — Recurrent Neural Network

- Entradas vêm da camada anterior **e do estado anterior** — modela sequências
- Analogo a diferença entre lógica combinatória e máquina de estados
- Usa: reconhecimento de fala, tradução

### LSTM — Long Short-Term Memory

- Tipo de RNN mais popular; resolve problema de memória de longo prazo
- Hierárquico: composto de **células** (templates interligados)
- Cada célula: 5 vector-matrix multiplies com 5 conjuntos únicos de pesos
- 3 **gates**: input gate, output gate, forget gate
- Memória de curto prazo (short-term) e longo prazo (cell state) passadas entre células
- Células conectadas da esquerda para direita; também "unrolled" no tempo

---

## Benchmarks de Inferência Google (2016)

Representam 95% do workload de inferência do Google:

| Nome | Tipo | Camadas | Pesos | Ops/peso |
|---|---|---|---|---|
| MLP0 | MLP | 5 | 20M | 200 |
| MLP1 | MLP | — | — | — |
| LSTM0 | LSTM | 5 | 852M | 64 |
| LSTM1 | LSTM | 5 | 634M | 96 |
| CNN0 | CNN | — | — | alta |
| CNN1 | CNN | — | — | alta |

- MLPs e LSTMs: **memory-bound** (baixa intensidade operacional)
- CNNs: **compute-bound** (alta intensidade operacional)

---

## Inferência vs Treinamento

| Aspecto | Inferência | Treinamento |
|---|---|---|
| Fase | Produção | Desenvolvimento |
| Pesos | Fixos (carregados) | Atualizados iterativamente |
| Dados | Um exemplo ou batch | Grandes datasets |
| Precisão | INT8 suficiente | FP16 ou FP32 necessário |
| Latência | Crítica (99th percentile) | Flexível (sem deadline duro) |
| Hardware típico | TPU, mobile DSA | GPU, Intel Crest, GPU clusters |

> [!note] Por que INT8 basta para inferência?
> Redes treinadas com FP32 geralmente mantêm acurácia ao converter pesos para INT8 — a informação já foi comprimida durante treinamento. Reduz área de chip e energia ~4× vs FP32.

---

## Relações (SPO)

- DNN → compõe-se de → camadas de neurônios artificiais
- MLP → opera via → vector-matrix multiply (fully connected)
- CNN → opera via → stencil 3D sobre feature maps
- LSTM → estende → RNN com gates e memória de longo prazo
- Treinamento → produz → pesos que fixam comportamento da rede
- Inferência → aplica → pesos fixos a novas entradas
- Intensidade operacional → determina → gargalo (memory vs compute)
- INT8 → basta para → inferência; FP16/FP32 → necessário para → treinamento

---

## Ver também

- [[TPU — Tensor Processing Unit]] — DSA Google para inferência de DNN
- [[Intel Crest]] — DSA Intel para treinamento de DNN
- [[GPU — Arquitetura SIMD]] — plataforma dominante para treinamento
- [[Arquitetura de Domínio Específico — DSA]] — contexto geral
- [[Modelo Roofline]] — análise de intensidade operacional vs bandwidth
- [[Instruções SIMD]] — operações vetoriais que aceleram DNNs em CPUs
