---
title: Desempenho de SMP
tags:
  - computação/arquitetura
  - multiprocessadores
aliases:
  - symmetric multiprocessor performance
  - snoop bandwidth
  - coherence miss rate
  - SMP scaling
date: 2026-04-22
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Desempenho de SMP

Análise quantitativa do desempenho de [[Multiprocessadores|multiprocessadores simétricos]] com snooping, com foco nos gargalos de banda de barramento e coerência.

> [!info] Fonte
> *Computer Architecture: A Quantitative Approach*, P&H 6ª ed., cap. 5, seção 5.3.

## Modelo de Desempenho

Para um multiprocessador com snooping baseado em barramento:

**Largura de banda de snoop necessária:**
$$
\text{BW}_{\text{snoop}} = N_{\text{proc}} \times \text{Clock} \times \text{CMR} \times \text{ciclos por miss}
$$

Exemplo (P&H): 8 processadores a 3 GHz, 15 ciclos por miss de coerência e capacidade de snoop de 6,4 GB/s:

$$
3{,}0 \text{ GHz} \times 8 \times \text{CMR} = 13{,}7 \times 10^9 \times \text{CMR}
$$

Para CMR = 0,73%, o sistema satura a largura de banda de snoop. Acima desse limiar, o barramento vira gargalo.

## Tipos de Miss em Multiprocessadores

| Tipo | Causa |
|---|---|
| **Miss uniprocessador** | cold, capacity, conflict (os 3 Cs) |
| **Miss de comunicação (CMR)** | dado compartilhado modificado por outro processador (coerência) |

CMR cresce com o número de processadores — comprometer escalabilidade.

## Técnicas para Escalar Banda de Snoop

### 1. Duplicação de Tags de Cache
- Tags duplicados → banco de snoop independente
- Processador e controlador de snoop operam em paralelo
- Reduz ciclos médios por CMR (~12,5 vs. 15)
- Suporta maior CMR ou mais processadores

### 2. L3 Compartilhado e Distribuído (NUCA)
- L3 dividido: cada processador gerencia uma fração do espaço de endereços
- Snoop de L3 escala linearmente com número de núcleos
- Exige **inclusividade**: L3 deve ser inclusivo de L2 para filtrar snoops
- Usado no IBM 12-core Power8

### 3. Diretório na L3 Compartilhada
- L3 atua como diretório de coerência em vez de protocolo de broadcast
- Elimina snoops desnecessários para linhas não compartilhadas

## Escalabilidade e Multicore

O fim do **Dennard Scaling** levou ao multicore para explorar o Moore's Law sem aumentar frequência. Mas:

- **Lei de Amdahl** limita ganho: fração serial domina com muitos núcleos
- **Dark silicon**: núcleos que não podem operar simultaneamente por constraints de energia
- CMR crescente com mais cores → snoop bandwidth vira gargalo

> [!warning] Dark Silicon
> Esmaeilzadeh et al. (2012): restrições de potência podem limitar escalabilidade de multicore assim como limitações de clock limitaram single-core.

## SMT × Multiprocessamento

Estudo com IBM Power5 (dual-core + SMT):
- Multithreading simultâneo ([[Multithreading no Chip|SMT]]) no multicore soma ganhos
- SPECintRate e SPECfpRate mostram ganhos reais ao combinar multicore + SMT
- Cuidado: dois threads com alta demanda de cache podem gerar thrashing (miss excessivo)

## Relações

- [[Multiprocessadores]] — taxonomia, UMA/NUMA, protocolos
- [[Coerência de Cache]] — MESI, snooping, write-back
- [[Protocolo de Diretório]] — alternativa escalável ao snooping para NUMA
- [[Falso Compartilhamento]] — CMR espúrio por layout de dados
- [[Multithreading no Chip]] — SMT complementa multiprocessamento
- [[Lei de Amdahl]] — limite teórico de escalabilidade
