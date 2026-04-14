---
title: Concorrência e Paralelismo
aliases:
  - concurrency
  - parallelism
  - concorrência
  - paralelismo
  - thread-level concurrency
  - instruction-level parallelism
  - ILP
  - SIMD
  - multicore
  - hyperthreading
  - simultaneous multithreading
tags:
  - computação/arquitetura
date: 2026-04-07
---

# Concorrência e Paralelismo

## Definições

| Conceito | Definição |
|---|---|
| **Concorrência** | Conceito geral: sistema com múltiplas atividades simultâneas (ou aparentemente simultâneas) |
| **Paralelismo** | Uso da concorrência para tornar o sistema *mais rápido* |

O paralelismo pode ser explorado em **múltiplos níveis de abstração**. Do nível mais alto ao mais baixo:

1. [[#Concorrência a nível de thread]] — múltiplos programas ou threads
2. [[#Paralelismo a nível de instrução]] — múltiplas instruções ao mesmo tempo no processador
3. [[#SIMD — Instrução única, múltiplos dados]] — uma instrução opera sobre múltiplos valores

## Relações (SPO)
- Paralelismo → usa → concorrência para aumentar desempenho
- Concorrência → suportada desde → timesharing (~1960)
- Paralelismo a nível de thread → requer → sistema multiprocessador ou multicore
- Paralelismo a nível de instrução → implementado em → [[Processador]] (pipeline, superescalar)
- SIMD → acelerada por → unidades vetoriais do [[Processador]]
- Lei de Amdahl → limita → ganho obtido com paralelismo

---

## Concorrência a nível de thread

### Sistemas uniprocessador vs. multiprocessador

| Tipo | Descrição |
|---|---|
| **Uniprocessor** | Um único processador; concorrência *simulada* por troca rápida de contexto |
| **Multiprocessor** | Múltiplos processadores sob um único kernel do SO; concorrência *real* |

Até recentemente, a maior parte do processamento era feita por um único processador, mesmo que este alternasse rapidamente entre tarefas — dando a ilusão de simultaneidade. Com o advento de sistemas multicore e hyperthreading, o paralelismo real tornou-se comum.

### Multi-core

Múltiplas CPUs (**núcleos**) integradas em um único chip de circuito integrado.

```
Pacote do processador
┌──────────────────────────────────────────────┐
│  Core 0          Core 1          Core 2 ...  │
│  ┌──────────┐   ┌──────────┐                 │
│  │L1 i-cache│   │L1 i-cache│   ...           │
│  │L1 d-cache│   │L1 d-cache│                 │
│  │L2 cache  │   │L2 cache  │                 │
│  └──────────┘   └──────────┘                 │
│         L3 cache (compartilhada)             │
└──────────────────────────────────────────────┘
              Memória principal
```

- Cada núcleo possui suas próprias caches L1 e L2
- A L1 de cada núcleo é dividida em **i-cache** (instruções) e **d-cache** (dados)
- A **L3 é compartilhada** entre todos os núcleos
- Especialistas prevêem dezenas, depois centenas de núcleos por chip no futuro

### Hyperthreading (simultaneous multi-threading)

Técnica que permite a **um único núcleo executar múltiplos fluxos de controle** (threads):

- **Múltiplas cópias** de partes do hardware: contadores de programa (PC), arquivos de registradores
- **Cópia única** de outras partes: unidades de ponto flutuante, cachês de dados

| | Processador convencional | Processador hyperthreaded |
|---|---|---|
| Troca de thread | ~20.000 ciclos de clock | Ciclo a ciclo |
| Estratégia | Troca de contexto | Decide por ciclo qual thread executar |
| Vantagem | Simplicidade | Aproveita melhor recursos ociosos (ex: espera de cache miss) |

**Exemplo (Intel Core i7):** cada núcleo pode executar 2 threads → sistema com 4 núcleos executa 8 threads em paralelo.

### Benefícios do multiprocessamento

1. **Reduz a necessidade de simular concorrência** — múltiplas tarefas rodam em núcleos distintos
2. **Acelera um único programa** — mas somente se o programa for expresso em múltiplas threads que possam executar em paralelo

> [!note] Relação com [[Processo#Threads|threads]]
> Threads são unidades de execução dentro de um processo que compartilham código e dados. O paralelismo a nível de thread explora múltiplos núcleos para executar essas threads verdadeiramente em paralelo.

---

## Paralelismo a nível de instrução

Processadores modernos executam **múltiplas instruções ao mesmo tempo** — propriedade conhecida como **ILP (Instruction-Level Parallelism)**.

### Evolução histórica

| Era | Taxa de execução |
|---|---|
| Intel 8086 (1978) | 3–10 ciclos por instrução |
| Processadores modernos | 2–4 instruções por ciclo |
| Com técnicas avançadas | até 100 instruções em voo simultaneamente |

### Pipeline

Técnica fundamental de ILP: as etapas para executar uma instrução são divididas em **estágios independentes** que operam em paralelo sobre instruções diferentes.

```
Instrução:   [Fetch] → [Decode] → [Execute] → [Write-back]
              ↑          ↑          ↑            ↑
             I4         I3          I2           I1     (ao mesmo tempo)
```

Um design de hardware relativamente simples pode sustentar taxa próxima de **1 instrução por ciclo** de clock.

> [!info] Relação com ISA vs. Microarquitetura
> O modelo sequencial (uma instrução por vez) é a [[ISA]] — o contrato público do processador com o software. O pipeline é uma técnica da **microarquitetura** — oculta do programador mas crítica para o desempenho.

### Superescalar

[[RISC e CISC#Superescalar|Processadores superescalares]] sustentam taxa **superior a 1 instrução por ciclo**. A maioria dos processadores modernos suporta operação superescalar. Programadores que entendem o modelo superescalar podem escrever código que gera maior grau de ILP.

---

## SIMD — Instrução única, múltiplos dados

No nível mais baixo de paralelismo: hardware especial que permite a uma **única instrução** realizar **múltiplas operações em paralelo**.

**Exemplo:** instruções das famílias Intel/AMD podem somar **8 pares de números de ponto flutuante de precisão simples** em uma única instrução.

| Característica      | Detalhe                                                                                          |
| ------------------- | ------------------------------------------------------------------------------------------------ |
| Alvo principal      | Processamento de imagem, áudio, vídeo                                                            |
| Acesso em C         | Tipos vetoriais em compiladores como gcc                                                         |
| Extração automática | Alguns compiladores detectam SIMD automaticamente; método mais confiável é programação explícita |
|                     |                                                                                                  |

---

## A [[Lei de Amdahl]] e os limites do paralelismo

Toda estratégia de paralelização esbarra na Lei de Amdahl: o ganho é limitado pela fração *sequencial* do programa — a parte que não pode ser paralelizada.

$$S_\infty = \frac{1}{1 - \alpha}$$

onde α é a fração paralelizável. Se 5% do programa é sequencial, o speedup máximo (com infinitos núcleos) é apenas **20×**.

---

---

## Arquiteturas Paralelas de Múltiplos Processadores

Para ganhos de 50× ou mais, pipeline e superescalar não bastam. Necessário: múltiplas CPUs. Três categorias principais:

### Processadores Vetoriais

Operam sobre **registradores vetoriais** — conjuntos de registradores carregados da memória em uma única instrução. Uma instrução de adição vetorial alimenta dois registradores vetoriais em um somador pipelined e produz um terceiro vetor. Toda a soma ocorre em **uma única unidade funcional** com alto grau de paralelismo interno.

- Aparentemente similar a [[#SIMD — Instrução única, múltiplos dados|SIMD]], mas a diferença está na implementação interna
- Exemplo clássico: **Cray-1** (1974) — computação científica
- Instruções SSE/AVX da Intel seguem modelo semelhante

### Multiprocessadores (fortemente acoplados)

Múltiplas CPUs totalmente desenvolvidas compartilham **memória em comum**. Qualquer CPU lê/escreve qualquer endereço.

- Coordenação via software para evitar conflitos
- Modelo de programação simples: memória compartilhada única
- Gargalo: barramento único sob contenção com muitos processadores
- Melhoria: memória local privada por processador reduz tráfego no barramento
- Escala moderada: ≤ 256 CPUs com relativa facilidade

### Multicomputadores (fracamente acoplados)

Vários computadores **sem memória compartilhada**, cada um com memória privada, interligados por rede.

- Comunicação por **troca de mensagens** (latência na ordem de microssegundos)
- Topologias: malha 2D/3D, árvores, anéis — mensagens passam por nós intermediários
- Mais fáceis de construir em larga escala (ex: IBM Blue Gene/P: >250.000 CPUs)
- Mais difíceis de programar — sem memória compartilhada transparente

### Comparação

| Aspecto | Multiprocessador | Multicomputador |
|---|---|---|
| Memória | Compartilhada | Privada por nó |
| Acoplamento | Forte | Fraco |
| Comunicação | Acesso direto à memória | Troca de mensagens |
| Facilidade de programação | Maior | Menor |
| Escalabilidade | Limitada (~256) | Alta (centenas de milhares) |
| Facilidade de construção | Menor | Maior |

---

## Ver também
- [[Desempenho do Processador]] — cache, previsão de desvios, superescalar, OoO, HT, clock dinâmico
- [[Multitarefa]] — timeslicing, TSS, preemptiva vs. cooperativa; multitarefa ≠ multicore
- [[Processo]] — abstração de processo e threads
- [[Processador]] — onde ILP e SIMD são implementados
- [[Lei de Amdahl]] — limite teórico dos ganhos de paralelismo
- [[RISC e CISC]] — superescalar como evolução do RISC
- [[Hierarquia de Memória]] — cache L3 compartilhada entre núcleos
- [[ISA]] — contrato público que o pipeline implementa de forma transparente
