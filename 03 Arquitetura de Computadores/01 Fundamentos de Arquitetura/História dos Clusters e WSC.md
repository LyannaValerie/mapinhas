---
title: História dos Clusters e WSC
aliases:
  - história dos clusters
  - cluster history
  - WSC history
  - história computação em nuvem
  - Beowulf
tags:
  - computação/arquitetura
  - computação/histórico
date: 2026-04-22
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# História dos Clusters e WSC

> Fonte: P&H Apêndice M.8

## Linha do Tempo

### Clusters — invenção (anos 1960)

- Clusters "inventados" por clientes que não cabiam todo o trabalho em uma máquina ou precisavam de backup
- Motivação original: **disponibilidade** (failover), não desempenho

### Tandem (1975)

- Primeiro cluster de 16 nós comercial
- Foco: alta disponibilidade, tolerância a falhas

### VAX Clusters — DEC (1984)

- Primeiro produto cluster de uma grande fabricante
- Computadores independentes com I/O compartilhado + SO distribuído de coordenação
- Depois: links de comunicação entre computadores + distribuição geográfica para sobreviver a desastres
- DEC (depois HP) vendeu mais de **25.000 clusters** até 1993

### Beowulf — NASA (1993–1994)

- Objetivo: 1 GFLOPS por menos de $50.000 (requisito NASA)
- 1994: 16 nós com Intel 80486 e Ethernet → meta atingida
- Fundou o cluster como alternativa ao [[Multicomputadores e Clusters|MPP]] para computação científica

### NOW — Network of Workstations (Berkeley, 1995)

- Projeto de pesquisa de Anderson, Culler e Patterson
- 1997: 100 UltraSPARC workstations + switches Myrinet 160 MB/s
- Recordes mundiais:
  - Ordenou **8,6 GB de disco em 1 minuto**
  - Quebrou chave DES de 40 bits em **3,5 horas**
- Desenvolveu o motor de busca **Inktomi**

> [!info] Por que o NOW foi fundacional para WSC
> Eric Brewer liderou o Inktomi usando hardware commodity + rede padrão por rack, demonstrando que infraestrutura de Internet podia ser construída sem SMPs caros. Google seguiu esse modelo quando superou o Inktomi.

### Google e a "Lei de Cluster Commodity"

- Google adotou o modelo Inktomi (não o de alta disponibilidade corporativa)
- Inovação inicial do Google: **qualidade de busca** (PageRank); inovações WSC vieram depois
- Estratégia: PCs Intel ~2 GHz, 4 GB RAM, 2 TB disco, Ethernet padrão
  - 80 PCs por estante; múltiplas estantes em centrais de dados
  - Switch Ethernet 128 portas por central; dois switches redundantes

**Três princípios do design Google:**
1. Componentes vão falhar — projete para a falha
2. Duplique tudo (hardware, dados, roteamento)
3. Otimize preço/desempenho, não apenas desempenho

### Infiniband e VI (Virtual Interface)

- Pesquisas de baixa latência em clusters geraram o padrão **VI Interface**
- VI foi adotado pelo **Infiniband** como base
- Infiniband: protocolo de alta velocidade para clusters e SANs

## Utility Computing → Cloud Computing

| Marco | Ano | Contexto |
|---|---|---|
| Timesharing | ~1960s | Primeira "utility computing" — vender tempo de CPU |
| Tandem clusters | 1975 | Alta disponibilidade como serviço |
| ASP (Application Service Provider) | ~1999 | Hospedar aplicações por assinatura |
| Amazon EC2 | 2006 | Primeira nuvem pública moderna |

> [!note] Raiz histórica
> Timesharing dos anos 1960 (vender ciclos de CPU) é o ancestral direto do modelo EC2/cloud. A ideia de que "computação pode ser utility como eletricidade" tem >50 anos.

## Contribuições Paralelas

### Tandem → HP

- Tandem: pioneiro em clusters HA; adquirida pela HP
- Produto sobreviveu como HP NonStop

### DEC VAX Clusters → HP

- Produto de clusters DEC também migrou para HP pós-aquisição

### IBM

- Linha contínua de produtos cluster, foco corporativo em alta disponibilidade

## Ciência em Clusters vs. MPPs

| Aspecto | Clusters (Beowulf/NOW) | MPPs (Blue Gene, Cray) |
|---|---|---|
| Hardware | Commodity (off-the-shelf) | Proprietário |
| Rede | Ethernet / Myrinet padrão | Rede proprietária de alta velocidade |
| Custo | Baixo | Muito alto |
| Programação | MPI padrão | MPI + extensions proprietárias |
| Escala | ~100–10.000 nós | 100.000+ nós |
| Uso | Computação científica geral | HPC científico de ponta |

A partir de 2000: clusters tornaram-se competitivos com MPPs para maioria das aplicações científicas. Custo/FLOP de cluster commodity superou MPPs em muitos benchmarks.

## Ver também

- [[Multicomputadores e Clusters]]
- [[WSC — Warehouse Scale Computer]]
- [[Computação em Nuvem]]
- [[Redes de Interconexão]]
- [[Multiprocessadores de Grande Escala]]
- [[História da Computação]]
- [[Lei de Amdahl]]
