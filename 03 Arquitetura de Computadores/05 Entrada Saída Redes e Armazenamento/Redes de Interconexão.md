---
title: Redes de Interconexão
aliases:
  - interconnection network
  - topologia de rede
  - largura de banda de bisseção
  - bisection bandwidth
  - hipercubo
  - hypercube
  - toro
  - grade de processadores
  - rede ômega
  - omega network
  - fat tree
  - árvore gorda
tags:
  - computação/arquitetura
date: 2026-04-13
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Redes de Interconexão

## Definição

Infraestrutura que conecta CPUs, memórias e nós em sistemas paralelos ([[Multiprocessadores]] e [[Multicomputadores e Clusters]]). Fundamental tanto para multiprocessadores (CPUs ↔ módulos de memória) quanto para multicomputadores (nós ↔ nós).

## Relações (SPO)
- Rede de interconexão → caracterizada por → topologia, diâmetro, largura de banda de bisseção, fanout
- Largura de banda de bisseção → limita → throughput de comunicação entre metades do sistema
- Hipercubo → tem → diâmetro logarítmico (= log₂n) com alto fanout
- Grade 2D → tem → diâmetro 2(√n − 1) e baixo custo de fiação
- Rede ômega → tem → (n/2)log₂n switches com roteamento determinístico
- Switch crossbar → é → sem bloqueio mas com custo O(n²)

---

## Métricas

| Métrica | Definição | Importância |
|---|---|---|
| **Diâmetro** | Maior distância (em saltos) entre dois nós quaisquer | Pior caso de latência |
| **Largura de banda de bisseção** | Mínima BW ao dividir a rede em duas metades iguais | Gargalo sob comunicação intensa |
| **Fanout (grau)** | Número de enlaces por nó | Tolerância a falhas, opções de roteamento |
| **Dimensionalidade** | Número de eixos independentes de roteamento | Opções de caminho |

> [!tip] Regra prática
> Você pode sempre comprar mais largura de banda (mais fios). Latência não tem esse luxo — otimize latência primeiro.

---

## Topologias

### Estrela (dimensão zero)
- Switch central conecta todos os nós
- Gargalo centralizado; falha única destrói o sistema

### Interconexão Total (malha completa)
- Cada nó conectado a todos os outros
- BW de bisseção máxima; diâmetro = 1
- Custo: k(k−1)/2 enlaces — inviável para k grande

### Árvore
- Simples; BW de bisseção = 1 enlace (gargalo no topo)
- **Árvore gorda (fat tree):** enlaces no topo têm maior BW (2b, 4b...) — elimina gargalo
- Usada no CM-5 (Thinking Machines)

### Anel (unidimensional)
- Cada nó conectado a dois vizinhos
- Diâmetro = n/2

### Grade 2D (bidimensional)
- Nós em grid; diâmetro = 2(√n − 1)
- Fácil de ampliar; muito usada comercialmente

### Toro duplo
- Grade com extremidades conectadas (wraps around)
- Diâmetro menor que grade; mais tolerante a falhas

### Toro 3D
- Extensão tridimensional do toro; cada nó tem 6 vizinhos
- Usado no BlueGene/P (toro 72×32×32) e Red Storm (toro 27×16×24)

### Hipercubo (n-dimensional)
- Construído recursivamente: dois hipercubos (n−1)-dimensionais com nós correspondentes conectados
- **Diâmetro = log₂(número de nós)** — excelente
- Fanout = dimensionalidade → custo alto em fios
- Comparação (1.024 nós): hipercubo diâmetro 10 vs. grade 32×32 diâmetro 62

```
Cubo 3D (8 nós):          Hipercubo 4D (16 nós):
   110──111                dois cubos 3D conectados
   /|   /|                 entre nós correspondentes
010──011 |
 | 100─|─101
 |/    |/
000──001
```

---

## Latência em Redes de Comutação

### Comutação de circuitos
$$L_{via} = T_s + p/b$$
onde $T_s$ = tempo de estabelecimento, $p$ = tamanho do pacote (bits), $b$ = largura de banda.

### Comutação de pacotes (store-and-forward)
$$L_{via} = T_a + n(p/b + T_d) + p/b$$
onde $T_a$ = overhead de montagem, $n$ = número de switches, $T_d$ = atraso por switch.

### Atalho virtual / Wormhole routing
$$L_{via} \approx T_a + p/b$$
Sem armazenamento intermediário completo — byte a byte é repassado adiante.

---

## Switch Crossbar

- Ponto de cruzamento em cada intersecção de linha de entrada × saída
- **Não bloqueante**: qualquer CPUi → qualquer Mj simultâneo (se Mj livre)
- Custo: n² pontos → viável até ~100 nós
- Exemplo: Sun Fire E25K usa crossbars 18×18 no plano central

---

## Rede Ômega

Organiza n/2 switches 2×2 por estágio, com log₂n estágios, padrão **embaralhamento perfeito**.

- Total de switches: (n/2)log₂n
- **Bloqueante**: dois caminhos podem colidir num switch
- Roteamento determinístico: bit k do campo Módulo → saída do estágio k
- Bits usados no roteamento são substituídos pelo caminho percorrido (para retorno da resposta)
- Memórias intercaladas (endereços consecutivos em módulos consecutivos) maximizam paralelismo

---

## Ver também
- [[Multiprocessadores]] — onde crossbar e ômega conectam CPUs a memórias
- [[Multicomputadores e Clusters]] — onde toros e hipercubos conectam nós MPP
- [[Coerência de Cache]] — protocolo que usa a rede para propagar invalidações
