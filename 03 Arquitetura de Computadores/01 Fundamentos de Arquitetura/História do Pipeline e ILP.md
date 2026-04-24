---
title: História do Pipeline e ILP
aliases:
  - história do pipeline
  - pipelining history
  - história ILP
  - desenvolvimento superescalar
tags:
  - computação/arquitetura
  - computação/histórico
date: 2026-04-22
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# História do Pipeline e ILP

> Fonte: P&H Apêndice M.5

## Marcos Históricos

### IBM Stretch (7030) — 1959, primeiro pipeline geral

- Primeiro processador de uso geral com [[Pipeline|pipeline]] real
- Objetivo: 100× mais rápido que o IBM 704 ("stretch" da fronteira tecnológica)
- Plano: fator 1,6 a partir de sobreposição de fetch/decode/execute em 4 estágios
- Introduziu **ALU bypasses** (forwarding) para lidar com dependências de dados
- Descrito por Bloch [1959] e Bucholtz [1962]

### CDC 6600 — 1964, scoreboard e múltiplas unidades funcionais

- Primeiro uso extensivo de **múltiplas unidades funcionais** (add, multiply, divide em paralelo)
- Introduziu **scoreboarding** — rastreamento dinâmico de disponibilidade de registradores
- [[Pipeline]] com execução **fora de ordem limitada**: instruções independentes avançam
- ISA load-store simples para facilitar o pipeline (influência direta no RISC)
- Processadores I/O usavam **multithreading fine-grained** para esconder latência
- Descrição em Thornton [1964, 1970]

> [!info] Conexão com [[Algoritmo de Tomasulo]]
> O CDC 6600 foi o precursor direto. Tomasulo (1967) generalizou e aprofundou as ideias de scoreboard no IBM 360/91.

### IBM 360/91 — 1967, Tomasulo e register renaming

- Primeiro processador com **[[Algoritmo de Tomasulo]]** completo:
  - Tagging de dados (tags nos operandos para dependência dinâmica)
  - **Register renaming** via reservation stations
  - Detecção dinâmica de hazards de memória
  - Forwarding generalizado
- Introduziu rudimentos de **branch prediction** (Anderson, Sparacio, Tomasulo [1967])
- Fracasso comercial: poucos vendidos, complexidade causou atraso — Model 85 (com cache) lançado antes e superou o 91
- Ideias ficaram dormentes por ~25 anos; ressurgidas nos anos 1990 em processadores superescalares

### RISC e pipelining — anos 1980

- RISCs projetados desde a concepção para facilitar pipelining
- Melhor análise: Bhandarkar e Clark [1991] — comparação quantitativa VAX vs. MIPS; provou vantagens do RISC após 10 anos de debate
- MIPS R4000: um dos primeiros microprocessadores **profundamente pipelinizados**
- DEC Alpha 21064: pipeline de inteiros similar ao R4000 + FP mais pipelinizado

### Branch Prediction — evolução

| Técnica | Autor/Ano | Mecanismo |
|---|---|---|
| 2-bit dynamic scheme | J.E. Smith [1981] | Contador de saturação 2 bits |
| Branch-target buffer / branch folding | Ditzel & McLellan [1987] | Armazena destino + instrução alvo |
| Correlating predictor | Pan, So, Rameh [1992] | Usa histórico de branches anteriores |
| Multilevel predictor | Yeh & Patt [1992, 1993] | Histórico individual por branch |
| Tournament predictor | McFarling [1993] | Combina predictor local + global |

### Superescalar — desenvolvimento

- **Projeto ACS (IBM, anos 1960, Califórnia):** multiple-issue, dynamic scheduling com backup registers, fetch por dois caminhos de branch. Cancelado. John Cocke foi força intelectual.
- **John Cocke + Tilak Agerwala** (IBM, meados 1980): cunharam o termo **superescalar**; proposta "America" → base do IBM Power1 (RS/6000)
- **IBM RS/6000 (1990):** primeira máquina **superescalar** comercial

### VLIW e LIW — tentativas paralelas

| Projeto | Empresa | Ano | Resultado |
|---|---|---|---|
| AP-120B | Floating Point Systems | 1981 | Pioneiro "push-and-catch" pipeline |
| VLIW (512 bits) | J. Fisher, Yale | 1983 | Teórico; trace scheduling |
| Multiflow | Multiflow | ~1987 | VLIW comercial; >100 vendidos; fracasso |
| Cydra 5 | Cydrome | ~1989 | VLIW com dynamic renaming; fracasso |

Problemas comuns: nova ISA de empresa pequena + competição de microprocessadores RISC que mudaram economia do mercado.

> [!note] Legado VLIW
> Multiflow e Cydrome falharam comercialmente mas produziram pesquisadores e tecnologias que foram incorporados em processadores posteriores. Influência direta em [[IA-64 e EPIC]] (Itanium).

### Multithreading — linha histórica

| Marco | Ano | Detalhes |
|---|---|---|
| TX-2 (MIT Lincoln Lab) | 1959 | Primeiro multithreading; threads para I/O rápido |
| CDC 6600 | 1964 | Fine-grained multithreading nos processadores I/O |
| HEP (Denelcor) | 1982 | Fine-grained multithreading para esconder latência de pipeline + memória |
| SPARCLE/Alewife | 1993 | Coarse-grained: troca de thread em cache miss |
| IBM Power4 + Pentium 4 Extreme | ~2003 | SMT (Simultaneous Multithreading) moderno |

### SMT — Simultaneous Multithreading

Insight dos anos 1990 (vários grupos independentes):
1. Fine-grained é melhor que coarse-grained para desempenho máximo
2. Para usar janelas de issue largas com eficiência, multiplas threads simultâneas são necessárias

Resultou em Hyperthreading (Intel) e SMT em PowerPC — ver [[Multithreading no Chip]].

### Era superescalar ampla (1994–2000)

Todos os grandes fabricantes anunciaram processadores superescalares wide (3+ issues/clock):
- Intel: Pentium Pro, Pentium II, Pentium III, Pentium 4
- AMD: K-5, K-6, Athlon
- Sun: UltraSPARC
- DEC: Alpha 21164, 21264
- MIPS: R10000, R12000
- IBM/Motorola: PowerPC 603/604/620

Período 2000–2005: maior clock via pipeline mais profundo (Pentium 4: ~20 estágios) + multithreading + início do multicore (IBM Power4, AMD Opteron, Intel).

## Contribuições do Compilador

- **Loop-level parallelism + dependence analysis:** D. Kuck e colegas (Univ. Illinois, anos 1970)
  - Cunharam: antidependência, output dependence
  - Testes GCD e Banerjee
- **Software pipelining (modulo scheduling):** B. Ramakrishna Rau [1994]
- **Trace scheduling:** J. Fisher [1981] — geração de código para VLIW
- **IMPACT framework:** Hwu e colegas (Illinois, início 1990s) — superblock scheduling + profiling

## Limites de ILP

Estudos teóricos de ILP disponível:
- Tjaden & Flynn [1970], Riseman & Foster [1972]: poucos ILP disponível → esfriou interesse por >10 anos
- Nicolau & Fisher [1984]: muito ILP em programas científicos com trace scheduling
- Wall [1991, 1993]: estudo mais abrangente; base para dados de ILP em P&H
- Tendência atual: janelas maiores precisam de melhor branch prediction para programas inteiros

## Ver também

- [[Pipeline]]
- [[Algoritmo de Tomasulo]]
- [[Escalonamento Dinâmico]]
- [[Previsão de Desvio]]
- [[Múltipla Emissão e Superescalar]]
- [[Especulação Baseada em Hardware]]
- [[VLIW — Very Long Instruction Word]]
- [[IA-64 e EPIC]]
- [[Multithreading no Chip]]
- [[Paralelismo em Nível de Instrução]]
- [[Execução Fora de Ordem]]
- [[História da Computação]]
