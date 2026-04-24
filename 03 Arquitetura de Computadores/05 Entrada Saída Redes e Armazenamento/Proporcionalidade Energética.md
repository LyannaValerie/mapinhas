---
title: Proporcionalidade Energética
aliases:
  - energy proportionality
  - dark silicon
  - silicon dark
  - proporcionalidade de energia
  - eficiência energética servidor
tags:
  - arquitetura/energia
  - arquitetura/desempenho
  - arquitetura/tecnologia
source: "P&H CA:AQA Cap. 1 §1.5"
date: 2026-04-23
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Proporcionalidade Energética

Conceito central para eficiência de servidores e WSCs: um sistema energeticamente proporcional consome potência proporcional ao trabalho realizado.

## Definição

Um servidor com **proporcionalidade energética perfeita** satisfaz:

$$P(\text{utilização}) = P_\text{pico} \times \text{utilização}$$

Na prática: servidores ociosos (10% de utilização) ainda consomem 50–70% da potência de pico. Isso é o contrário de proporcional.

> [!example] Impacto em WSC
> Um data center com 50.000 servidores operando na média a 20% de utilização, mas consumindo 60% da potência de pico, desperdiça 40% do orçamento de energia em trabalho não realizado.

## Por que a Proporcionalidade é Difícil

### Consumo estático dominante em repouso

A potência total de um chip tem dois componentes:

$$P_\text{total} = P_\text{dynamic} + P_\text{static}$$

$$P_\text{dynamic} = \frac{1}{2} \cdot C \cdot V^2 \cdot f \quad ; \quad P_\text{static} = I_\text{static} \cdot V$$

- $P_\text{dynamic}$ escala com carga (frequência de chaveamento)
- $P_\text{static}$ existe **independente de carga** — corrente de fuga sempre presente

Em feature sizes ≤ 16 nm, $P_\text{static}$ representa fração crescente do total, tornando mais difícil reduzir consumo proporcional ao trabalho.

### Componentes periféricos não escalam

DRAM, storage e circuitos de I/O também consomem potência em repouso, independentemente da carga de CPU.

## Dark Silicon

Consequência do fim do [[Lei de Moore|escalonamento de Dennard]]: chips modernos possuem mais transistores do que podem ligar simultaneamente dentro do orçamento térmico (TDP).

**Dark silicon**: fração do chip que precisa permanecer desligada (clock-gated ou power-gated) para não exceder o TDP.

$$\text{TDP} \geq P_\text{transistores ativos} = \frac{1}{2} \cdot C_\text{ativo} \cdot V^2 \cdot f$$

Se todos os transistores fossem ligados simultaneamente, o chip aqueceria além do limite de dissipação do pacote.

### Implicações de design

1. **Specialização**: transistores extras usados para aceleradores de domínio específico (GPU on-chip, neural engines, codecs de vídeo) — ligados apenas quando relevantes
2. **Turbo Boost**: liga transistores extras temporariamente quando TDP permite (carga leve, temperatura baixa)
3. **Heterogeneidade**: big.LITTLE / ARM DynamIQ — núcleos eficientes sempre ativos; núcleos potentes ligados sob demanda

## Estratégias para Aumentar Proporcionalidade

| Estratégia | Mecanismo | Trade-off |
|---|---|---|
| **DVFS** | Reduz V e f com carga → $P \propto V^2 f$ | Não elimina $P_\text{static}$ |
| **Power gating** | Corta alimentação de subsistemas inativos | Latência para religar |
| **Consolidação** | Rodar menos servidores a carga alta | Menos servidores ociosos |
| **Scale-out** | Ligar/desligar servidores inteiros | Granularidade grossa |
| **Aceleradores** | Usar hardware eficiente para tarefa específica | Menor utilização por chip |

> [!tip] Regra de ouro em WSC
> Custo de reduzir 1 W de consumo: ~$2/Watt-ano. Qualquer técnica que economize mais de $2/W-ano em infraestrutura vale o investimento.

## Métricas de Eficiência Energética

**PUE (Power Utilization Effectiveness):**

$$\text{PUE} = \frac{\text{Total Facility Power}}{\text{IT Equipment Power}}$$

- PUE ideal = 1,0 (toda energia vai para TI)
- PUE típico ~2012: 1,5–2,0
- PUE moderno (Google, hyperscalers): 1,1–1,2

**SPECpower:**

$$\text{ssj\_ops/watt} = \frac{\text{server-side Java ops/s}}{\text{Potência média (W)}}$$

Mede proporcionalidade: benchmark varia carga de 0% a 100% e registra eficiência em cada ponto.

## Relação com Amdahl e Desempenho

Proporcionalidade energética é o equivalente energético da [[Lei de Amdahl]]: a fração do sistema que não escala com a carga limita a eficiência total, assim como a fração não paralelizável limita o speedup.

$$\text{Eficiência}(\text{util}) = \frac{\text{util} \times P_\text{pico}}{P_\text{static} + \text{util} \times (P_\text{pico} - P_\text{static})}$$

A eficiência sobe com a utilização — argumento para operar servidores próximos do pico em vez de subutilizados.

## Ver também

- [[Gerenciamento de Consumo Elétrico]] — DVFS, estados C/P, fórmulas de potência dinâmica/estática
- [[Lei de Moore]] — fim do escalonamento de Dennard como causa raiz
- [[Classes de Computadores]] — WSC/servidor como contexto principal desta preocupação
- [[Benchmarks de Desempenho]] — SPECpower como benchmark de proporcionalidade
- [[WSC — Warehouse Scale Computer]] — PUE e custo operacional em escala
- [[Arquitetura de Domínio Específico — DSA]] — dark silicon → especialização como resposta
