---
title: Multiprocessadores de Grande Escala
aliases:
  - large-scale multiprocessors
  - MPP
  - massively parallel processors
  - scientific applications multiprocessor
  - true parallel computing
  - Appendix I
tags:
  - computação/arquitetura
  - computação/paralelismo
date: 2026-04-22
source: "P&H Apêndice I"
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Multiprocessadores de Grande Escala

Sistemas com centenas a milhares de processadores para **computação paralela verdadeira** — aplicações científicas e técnicas onde tarefas cooperam intensamente numa mesma computação.

## Relações (SPO)

| Sujeito | Predicado | Objeto |
|---|---|---|
| computação paralela verdadeira | contrasta com | multiprogramação (tarefas independentes) |
| aplicações científicas | requerem | alta taxa de comunicação inter-tarefas |
| clusters loosely coupled | são adequados para | web servers e OLTP (baixa comunicação) |
| latência de comunicação | afeta diretamente | desempenho e programabilidade |
| MPP | difere de | SMP centralizado (escalabilidade) |
| [[Protocolo de Diretório]] | escala melhor que | snooping em grande escala |

## Computação Paralela Verdadeira vs. Multiprogramação

| Aspecto | Computação Paralela Verdadeira | Multiprogramação |
|---|---|---|
| Tarefas | Cooperam numa aplicação | Independentes |
| Comunicação | Alta frequência, dados compartilhados | Rara ou via SO |
| Sincronização | Crítica para correção e desempenho | Mínima |
| Alvo | Aplicações científicas/técnicas | Web, OLTP, desktop |
| Acoplamento necessário | Forte (rede rápida, memória compartilhada) | Fraco (clusters OK) |

## Características de Desempenho de Programas Paralelos

Além de cache behavior e ILP (típicos de programas sequenciais), programas paralelos têm:

1. **Quantidade de paralelismo** — fração paralelizável (Lei de Amdahl limita)
2. **Tamanho das tarefas paralelas** — granularidade fina vs. grossa
3. **Frequência e natureza da comunicação inter-tarefas**
4. **Frequência e natureza da sincronização**

Ambos afetados pela natureza da aplicação **e** pelo estilo de programação.

## Métricas de Performance de Comunicação

```
Latência = overhead_emissor + time_of_flight + transmission_time + overhead_receptor
```

| Métrica | Definição |
|---|---|
| **Bandwidth** | Limitado idealmente por memória/processador/rede (não pelo mecanismo) |
| **Latência** | Soma dos 4 componentes acima; afeta desempenho E programabilidade |
| **Ocultação de latência** | Capacidade de sobrepor comunicação com computação |

## Desafios de Escalabilidade

Em relação a SMPs de pequena escala:

- **Sincronização:** mais tarefas → contention em locks/barreiras mais grave → necessidade de [[Sincronização em Multiprocessadores#Queuing Locks|queuing locks]] e barreiras em árvore
- **Coerência:** snooping não escala para centenas de nós → [[Protocolo de Diretório]]
- **NUMA:** memória distribuída → acessos remotos muito mais lentos que locais
- **Hot spots:** uma localização de memória acessada por muitos processadores → gargalo
- **False sharing:** [[Falso Compartilhamento]] agravado com mais processadores

## Workloads Científicos Típicos (Appendix I)

### Ocean Simulation
- Grade 2D de pontos, stencil computation
- Alta localidade espacial; blocking por cache essencial ([[Otimizações Avançadas de Cache]])
- Comunicação nas bordas das partições

### FFT (Fast Fourier Transform)
- Alta taxa de comunicação all-to-all
- Cada processador precisa de dados de todos os outros na segunda metade
- Communication-to-computation ratio cresce com tamanho do problema

### Álgebra Linear (multiplicação de matrizes)
- Blocking por cache [[Otimizações Avançadas de Cache|(loop tiling)]] reduz comunicação drasticamente
- Computation-to-communication ratio alto → bem adequado para paralelo
- Exemplo: BlueGene/P e Cray Red Storm usam para benchmarks ([[Multicomputadores e Clusters]])

## Taxonomy de Memória em Grande Escala

| Modelo | Característica | Exemplos |
|---|---|---|
| SMP / UMA | Memória fisicamente centralizada; acesso uniforme | SGI Origin (pequeno) |
| DSM / NUMA | Memória distribuída; acesso não-uniforme; endereço global | Origin 2000, T3D |
| MPP / cluster | Sem espaço de endereços global; message passing | BlueGene, Cray |

[[Protocolo de Diretório]] é a solução de coerência para DSM.

## Ver também

- [[Multiprocessadores]] — SMP centralizado (pequena escala)
- [[Multicomputadores e Clusters]] — arquiteturas MPP e clusters
- [[Protocolo de Diretório]] — coerência para DSM em grande escala
- [[Sincronização em Multiprocessadores]] — locks/barreiras escaláveis
- [[Falso Compartilhamento]] — problema de cache em paralelo
- [[Lei de Amdahl]] — limita speedup máximo independente de escala
