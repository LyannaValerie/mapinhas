---
title: Multiprocessadores
aliases:
  - shared memory multiprocessor
  - SMP
  - UMA
  - NUMA
  - COMA
  - CC-NUMA
  - NC-NUMA
  - multiprocessador de memória compartilhada
  - consistência de memória
  - crossbar switch
  - rede ômega
tags:
  - computação/arquitetura
date: 2026-04-13
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Multiprocessadores

## Definição

Computador paralelo com **memória física compartilhada**: todas as CPUs acessam o mesmo espaço de endereços com LOAD/STORE. Distingue-se do [[Multicomputadores e Clusters|multicomputador]] pela ausência de troca de mensagens explícita.

## Relações (SPO)
- Multiprocessador → compartilha → memória física entre todas as CPUs
- UMA → garante → tempo de acesso uniforme a todos os módulos de memória
- NUMA → tem → tempo de acesso variável (local < remoto)
- COMA → trata → memória principal como cache gigante
- Protocolo de coerência → necessário em → qualquer multiprocessador com caches
- Switch crossbar → é → rede sem bloqueio com O(n²) pontos de cruzamento
- Rede ômega → é → rede bloqueante com O(n/2 × log n) switches

---

## Taxonomia: UMA × NUMA × COMA

| Tipo | Tempo de acesso | Cache | Localidade importa? |
|---|---|---|---|
| **UMA** | Uniforme | Sim | Não |
| **NC-NUMA** | Não uniforme | Não | Muito |
| **CC-NUMA** | Não uniforme | Sim (coerente) | Moderadamente |
| **COMA** | Não uniforme | Memória = cache | Automático |

---

## UMA — Multiprocessadores com Barramento Único

Arquitetura mais simples: barramento compartilhado + caches locais + [[Coerência de Cache|protocolo MESI]].

```
CPU₀   CPU₁   CPU₂  ...  CPUₙ
 |C|    |C|    |C|         |C|     ← caches locais
 ├──────┴──────┴─── ... ───┤
           Barramento
 ├──────────────────────────┤
         Memória principal
```

- Limite prático: **16–32 CPUs** (barramento vira gargalo)
- Variante: memória privada por CPU + barramento só para dados compartilhados

### Switch Crossbar

Conecta n CPUs a k memórias com **ponto de cruzamento** em cada intersecção.

```
        M₀₀₀  M₀₀₁  M₀₁₀  ...  M₁₁₁
CPU₀₀₀  ×     ·     ·          ·
CPU₀₀₁  ·     ·     ·          ·
CPU₀₁₀  ·     ·     ×          ·
...
CPU₁₁₀  ·     ×     ·          ·
```

- **Sem bloqueio**: qualquer CPU pode conectar a qualquer memória livre
- **Custo:** n² pontos de cruzamento → inviável para n grande (>100 CPUs)

### Rede Ômega (Multiestágio)

log₂n estágios de switches 2×2 com padrão de embaralhamento perfeito.

- Switches totais: (n/2) × log₂n — muito melhor que n²
- **Com bloqueio**: requisições concorrentes podem colidir em um switch
- Roteamento: cada estágio usa 1 bit do campo Módulo (MSB primeiro)
- Resposta: bits do caminho percorrido são armazenados no campo Módulo

**Comparação (n=1024):**

| Rede | Switches/pontos |
|---|---|
| Crossbar | 1.048.576 pontos de cruzamento |
| Ômega | 5.120 switches 2×2 |

---

## Modelos de Consistência de Memória

Contrato entre software e hardware sobre o comportamento de operações concorrentes.

| Modelo | Garantia | Custo de implementação |
|---|---|---|
| **Consistência estrita** | Toda leitura retorna escrita mais recente | Impossível com cache |
| **Consistência sequencial** | Existe uma ordem global de todas as operações, vista por todos | Alta |
| **Consistência de processador** | Escritas de cada CPU vistas em ordem por todos; ordem entre CPUs diferentes pode variar | Moderada |
| **Consistência fraca** | Sem ordem garantida; operações de sincronização delimitam épocas | Baixa |
| **Consistência de liberação** | acquire/release delimitam seções críticas; escritas concluem antes do próximo acquire | Prática |

---

## NUMA — Acesso Não Uniforme à Memória

Cada CPU tem memória local (acesso rápido); acesso à memória de outra CPU é mais lento.

### NC-NUMA (sem cache)
- Sem problema de coerência (cada palavra em exatamente um lugar)
- Scanner de páginas move páginas para próximo de quem as usa
- Penalidade por acesso remoto: ~10× mais lento
- Exemplo histórico: **Carnegie-Mellon Cm\***

### CC-NUMA (com cache coerente)
- Cache presente → coerência necessária → **diretório por nó**
- Diretório rastreia onde cada linha de cache está armazenada
- Acesso remoto: requisição via rede → nó nativo consulta diretório → envia dado
- Overhead do diretório: ~1–2% da memória (ex: 9 bits × 2¹⁸ entradas / 16 MB ≈ 1,76%)
- Exemplo: **Sun Fire E25K** (72 chips UltraSPARC IV, switches crossbar 18×18, escuta local + diretório remoto)

---

## COMA — Acesso Somente à Memória Cache

Memória principal de cada nó funciona como **cache de atração** (attraction memory): blocos migram conforme demanda, sem residência fixa.

- Elimina o problema de posicionamento de páginas do NUMA
- **Problema 1:** localizar uma linha sem máquina nativa (busca na árvore ou broadcast)
- **Problema 2:** ao expulsar a última cópia de uma linha, não pode simplesmente descartá-la
- Promete melhor desempenho que CC-NUMA para cargas com muito acesso remoto
- Exemplos: KSR-1, Data Diffusion Machine

---

## SMP — Multiprocessador Simétrico

Variante de UMA onde cada CPU tem **igual acesso** a todos os módulos de memória e dispositivos de E/S, e o SO as trata como intercambiáveis.

---

## Ver também
- [[Coerência de Cache]] — protocolo MESI, snooping, write-back, snooping bandwidth
- [[Protocolo de Diretório]] — coerência para NUMA sem broadcast
- [[Sincronização em Multiprocessadores]] — TAS, LL/SC, barreiras
- [[Consistência de Memória]] — SC, TSO, modelos relaxados
- [[Falso Compartilhamento]] — miss de coerência por layout de dados
- [[Multicomputadores e Clusters]] — memória privada, troca de mensagens
- [[Concorrência e Paralelismo]] — taxonomia Flynn, visão geral
- [[Redes de Interconexão]] — topologias para conectar CPUs e memórias
- [[Multithreading no Chip]] — paralelismo dentro de um único chip
- [[Lei de Amdahl]] — limite de escalabilidade
