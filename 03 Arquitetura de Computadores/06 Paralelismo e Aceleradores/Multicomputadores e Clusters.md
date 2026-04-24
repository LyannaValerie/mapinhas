---
title: Multicomputadores e Clusters
aliases:
  - multicomputer
  - message passing
  - MPP
  - massively parallel processor
  - cluster
  - BlueGene
  - Red Storm
  - MPI
  - DSM
  - distributed shared memory
  - Linda
  - Orca
  - troca de mensagens
  - computação de cluster
tags:
  - computação/arquitetura
date: 2026-04-13
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Multicomputadores e Clusters

## Definição

Computadores paralelos onde cada CPU tem **memória privada**. Comunicação via **troca de mensagens explícita** (send/receive). Distingue-se do [[Multiprocessadores|multiprocessador]] pela ausência de memória compartilhada em hardware.

## Relações (SPO)
- Multicomputador → comunica-se por → troca de mensagens (send/receive)
- MPP → é → multicomputador de alto custo com rede proprietária de alta velocidade
- Cluster → é → multicomputador com componentes de prateleira
- MPI → é → biblioteca padrão de troca de mensagens para multicomputadores
- DSM → simula → memória compartilhada em software sobre hardware de multicomputador
- Linda → usa → espaço de tuplas como abstração de memória compartilhada
- Orca → usa → objetos compartilhados replicados com consistência sequencial

---

## Multiprocessadores vs. Multicomputadores

| Aspecto | Multiprocessador | Multicomputador |
|---|---|---|
| Memória | Compartilhada | Privada por nó |
| Comunicação | LOAD/STORE | send()/receive() |
| Facilidade de programar | Alta | Baixa |
| Escalabilidade | ~256 CPUs | Centenas de milhares |
| Facilidade de construir | Menor | Maior |
| Exemplo | Sun Fire E25K | IBM BlueGene/P |

---

## MPPs — Processadores Maciçamente Paralelos

Supercomputadores com milhares de CPUs conectados por rede proprietária de altíssimo desempenho.

### IBM BlueGene/P (2007)

| Parâmetro | Valor |
|---|---|
| CPUs de cálculo | 294.912 |
| Chip | 4 núcleos PowerPC 450 @ 850 MHz |
| Memória por nó | 512 MB DDR2 |
| Memória total | 144 TB |
| Interconexão | Toro 3D 72×32×32 @ 3,4 Gbps/enlace |
| Pico | ~1 petaflop/s |
| Redes adicionais | Árvore combinada, barreira, Gigabit Ethernet |

- Filosofia: chip barato + baixo consumo → maior sistema possível
- ~371 megaflops/W (eficiência energética quase 2× predecessor BlueGene/L)
- Roteamento por **atalho virtual** no toro 3D

### Cray Red Storm (2004, atualizado 2006)

| Parâmetro | Valor |
|---|---|
| CPUs de cálculo | 20.736 (Opteron dual-core 2,4 GHz) |
| Memória por nó | 2–4 GB |
| Memória total | 10 TB |
| Roteador | Seastar (IBM), toro 3D 27×16×24 |
| Latência ponto a ponto | 2 μs (adjacente), 5 μs (pior caso) |
| Pico | ~41 teraflops/s |
| Espaço em disco | 240 TB |

- Filosofia: CPU poderosa de prateleira + roteador especializado de alto desempenho
- Pode ser particionado entre seções confidencial e não confidencial

---

## Clusters

Computadores comuns (PCs/estações) conectados por rede comercial.

### Google (exemplo de cluster em escala web)

- PCs Intel ~2 GHz, 4 GB RAM, 2 TB disco, Ethernet
- Empilhados: 80 PCs/estante; estantes em centrais de dados
- Topologia: switch Ethernet 128 portas por central de dados, dois switches redundantes
- Princípios: **(1)** componentes falham — planeje a falha; **(2)** duplique tudo; **(3)** otimize preço/desempenho
- RAID e replicação de fragmentos distribuídos por múltiplas máquinas

---

## MPI — Interface de Troca de Mensagens

Biblioteca padrão de comunicação para multicomputadores.

### Conceitos principais
- **Comunicador**: grupo de processos + contexto (evita interferência entre mensagens não relacionadas)
- **Tipos de dados**: char, int, long, float, double, tipos derivados
- **Topologias virtuais**: árvore, anel, grade, toro — nomeiam caminhos de comunicação

### Primitivas básicas
```c
MPI_Send(buffer, quant, tipo, destino, rótulo, comunicador)
MPI_Recv(&buffer, quant, tipo, origem, rótulo, comunicador, &status)
```

### Modos de comunicação
| Modo | Comportamento |
|---|---|
| **Síncrono** | Remetente bloqueia até receptor executar Recv |
| **Com buffer** | Mensagem vai para fila; remetente continua |
| **Padrão** | Dependente da implementação |
| **Pronto** | Remetente declara receptor disponível (sem verificação) |

Cada modo existe em versão **com bloqueio** e **sem bloqueio** (8 primitivas de envio, 2 de recepção).

### Comunicação coletiva
- Broadcast, scatter/gather, all-to-all, redução (soma, max...), barreira
- Todos os processos do grupo devem chamar com argumentos compatíveis

---

## Memória Compartilhada no Nível de Aplicação

Abordagens para dar ilusão de memória compartilhada em multicomputadores.

### DSM — Memória Compartilhada Distribuída (baseada em páginas)
- Espaço de endereço virtual único mapeado para páginas em diferentes nós
- Falta de página → SO localiza e transfere a página do nó remoto
- **Falso compartilhamento:** dois processos escrevem extremidades opostas da mesma página → ping-pong
- **Treadmarks:** usa consistência de liberação + rastreamento de diffs (só words modificadas são enviadas)

### Linda (espaço de tuplas)
- Espaço de tuplas global acessível por todos os processos:
  - `out(...)` — insere tupla
  - `in(...)` — remove tupla por conteúdo (bloqueia se não existe)
  - `read(...)` — lê sem remover
  - `eval(...)` — avalia em paralelo e insere resultado
- Matching por conteúdo: tipo + valor de campos constantes; `?var` captura campo
- Paradigma comum: **sacola de tarefas** (task bag) para balanceamento dinâmico

### Orca (objetos compartilhados)
- Objetos com estado interno + métodos com guardas booleanas
- Operações são **atômicas e sequencialmente consistentes**
- Estado: cópia única (em um nó) ou replicado (em todos os nós que o usam)
- Atualização de objeto replicado: número de sequência global → todas as cópias executam em mesma ordem
- Sincronização por **guardas** (bloqueia se guarda falsa) + exclusão mútua automática

---

## Escalonamento de Jobs

| Algoritmo | Descrição |
|---|---|
| **FIFO estrito** | Executa na ordem de chegada; pode bloquear jobs pequenos atrás de grandes |
| **Sem bloqueio de cabeça** | Pula jobs que não cabem; executa primeiro que cabe |
| **Lajotas (gang scheduling)** | Jobs especificam CPUs × tempo; escalonador encaixa como puzzle |

---

## Desempenho e Lei de Amdahl

Para um programa com fração f sequencial e (1−f) paralelizável em n CPUs:

$$\text{Speedup} = \frac{n}{1 + (n-1)f}$$

- Para f = 0: speedup linear (ideal)
- Para f > 0: teto rígido mesmo com n → ∞

---

## Ver também
- [[Multiprocessadores]] — memória compartilhada em hardware
- [[Computação em Grade]] — nível seguinte: distribuída via Internet, OGSA, Globus
- [[Redes de Interconexão]] — topologias (toro, hipercubo, grade)
- [[Lei de Amdahl]] — limite de speedup
- [[Concorrência e Paralelismo]] — visão geral do espaço de arquiteturas paralelas
