---
title: Lei de Amdahl
aliases:
  - Amdahl's law
  - lei de Amdahl
  - speedup
  - aceleração de desempenho
tags:
  - computação/arquitetura
date: 2026-04-07
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Lei de Amdahl

## Definição
Observação de **Gene Amdahl** (pioneiro da computação) sobre a eficácia de melhorar apenas uma parte de um sistema. Descreve como o ganho de desempenho de uma melhoria localizada é limitado pela fração do sistema que *não* foi melhorada.

## Relações (SPO)
- Lei de Amdahl → limita → ganho de desempenho de melhorias parciais
- Lei de Amdahl → formulada por → Gene Amdahl (pioneiro da computação)
- Lei de Amdahl → aplica-se a → sistemas, manufatura, GPA — qualquer processo com partes independentes

---

## A fórmula

Seja:
- $T_{old}$ — tempo original de execução do sistema completo
- $\alpha$ — fração do tempo original consumida pela parte a ser melhorada (0 < α < 1)
- $k$ — fator de melhoria da parte em questão (k > 1)

O novo tempo de execução é:

$$T_{new} = (1 - \alpha) \cdot T_{old} + \frac{\alpha \cdot T_{old}}{k} = T_{old} \left[(1 - \alpha) + \frac{\alpha}{k}\right]$$

O **speedup** (aceleração) geral do sistema é:

$$S = \frac{T_{old}}{T_{new}} = \frac{1}{(1 - \alpha) + \alpha/k}$$

---

## Exemplo numérico

Parte que consome **60% do tempo** (α = 0,6), acelerada por um fator de **3×** (k = 3):

$$S = \frac{1}{(1 - 0{,}6) + 0{,}6/3} = \frac{1}{0{,}4 + 0{,}2} = \frac{1}{0{,}6} \approx 1{,}67\times$$

> [!warning] A armadilha do speedup parcial
> Mesmo acelerando **60%** do sistema por **3×**, o ganho total é apenas **1,67×**. A parte não melhorada (40%) limita o resultado.

---

## Caso especial: k → ∞

Se a parte melhorada se torna **instantânea** (tempo tende a zero):

$$S_\infty = \frac{1}{1 - \alpha}$$

Para α = 0,6:

$$S_\infty = \frac{1}{1 - 0{,}6} = \frac{1}{0{,}4} = 2{,}5\times$$

Mesmo eliminando **completamente** 60% do tempo de execução, o ganho máximo é apenas **2,5×**.

---

## Insight central

> **Para acelerar significativamente o sistema como um todo, é preciso melhorar uma fração muito grande do sistema.**

Melhorias em partes menores — mesmo drásticas — têm impacto limitado. Esse princípio guia decisões de otimização em todos os níveis:

| Contexto | Aplicação |
|---|---|
| Hardware | Identificar qual estágio do pipeline limita o throughput |
| Software | Profiling: encontrar onde o programa gasta a maior parte do tempo |
| Manufatura | Qual etapa da linha de produção é o gargalo? |
| Estudo | Em qual matéria investir mais tempo para melhorar o CRA? |

---

## Problemas resolvidos

### Problema 1.1 — Caminhão em Montana
Viagem de 2.500 km, média de 100 km/h → 25 horas total.

**A. Montana (1.500 km) sem limite de velocidade, 150 km/h:**
- Montana: 1.500/150 = **10 horas**
- Resto da viagem: 1.000/100 = **10 horas**
- $S = 25/(10 + 10) = \mathbf{1{,}25\times}$

Equivalente: α = 0,6 (Montana), k = 1,5 → $S = 1/[0{,}4 + 0{,}6/1{,}5] = 1{,}25$

**B. Qual velocidade para S = 1,67×?**
- Tempo alvo: 25/1,67 ≈ **15 horas**
- Tempo disponível para Montana: 15 − 10 = **5 horas**
- Velocidade necessária: 1.500/5 = **300 km/h**

### Problema 1.2 — Motor de carro
Meta: melhoria de **4×** no desempenho geral. Apenas **90%** do motor pode ser melhorado (α = 0,9). Quanto deve ser melhorado esse 90%?

$$4 = \frac{1}{(1 - 0{,}9) + 0{,}9/k} \implies 0{,}1 + \frac{0{,}9}{k} = 0{,}25 \implies k = \frac{0{,}9}{0{,}15} = \mathbf{6\times}$$

Para atingir 4× de melhoria geral, os 90% melhoráveis precisam ser acelerados em **6×**.

> [!tip] Lição dos exemplos
> Atingir melhorias ambiciosas exige acelerar partes grandes *e* por fatores altos. Um componente que representa apenas 10% do tempo nunca pode ser o alvo principal de otimização.

---

## Implicação para paralelismo

A lei de Amdahl também limita os ganhos da [[Concorrência e Paralelismo|paralelização]]: se apenas uma fração α de um programa pode ser paralelizada, o speedup máximo (com infinitos processadores) é $1/(1-\alpha)$. Para α = 0,95, o limite é apenas 20×, independentemente do número de núcleos.

---

## Ver também
- [[Concorrência e Paralelismo]] — paralelismo como estratégia de aceleração
- [[Processador]] — onde as melhorias de desempenho se aplicam
- [[Hierarquia de Memória]] — gargalos de memória como alvo de otimização
