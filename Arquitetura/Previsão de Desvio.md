---
title: Previsão de Desvio
tags:
  - arquitetura
  - pipeline
  - desempenho
aliases:
  - Branch Prediction
  - Predição de Desvio
---

# Previsão de Desvio

Técnica usada por processadores com [[Pipeline]] para especular o caminho tomado por instruções de desvio condicional antes que a condição seja calculada. Evita bolhas (stalls) de pipeline pelo custo de possível recuperação.

## Relações SPO

| Sujeito | Predicado | Objeto |
|---|---|---|
| Previsão de Desvio | resolve problema de | hazard de controle no pipeline |
| Previsão errada | causa | flush do pipeline + penalidade de ciclos |
| BTB | armazena | endereços de destino de desvios anteriores |
| Previsão dinâmica | usa | histórico de execuções anteriores |

## Previsão Estática

Não usa histórico — decide na compilação ou por heurística fixa:

| Estratégia | Sigla | Taxa de acerto típica | Regra |
|---|---|---|---|
| **Sempre tomado** | AT | ~60% | prevê salto sempre; melhor para laços |
| **Nunca tomado** | NT | ~40% | continua sequencial; simples |
| **Backward Taken, Forward Not Taken** | BTFNT | ~65% | desvio para endereço menor → tomado; maior → não tomado |
| **Por profiling** | — | variável | compilador anota hints com dados de execuções reais |

> [!note] BTFNT
> Exploita o fato de que laços têm desvio backward (fechamento do laço) e são executados muitas vezes. Ifs geralmente são forward e menos frequentemente tomados.

A heurística de direção (BTFNT) acerta ~65% em código típico.

## Previsão Dinâmica de 1 Bit

Tabela indexada pelo PC (bits baixos) com 1 bit por entrada:
- `1` = última vez foi tomado → prevê tomado
- `0` = última vez não foi tomado → prevê não tomado

Problema: em laço com N iterações, erra 2× por laço (entrada e saída).

```
Estado:   0 ──tomado──▶ 1
          1 ──não tom──▶ 0
```

## Previsão Dinâmica de 2 Bits (FSM de 4 estados)

Corrige problema do 1-bit: necessita 2 erros consecutivos para mudar previsão.

```
         não tomado          não tomado
 ┌──────────────────┐  ┌──────────────────┐
 ▼                  │  ▼                  │
[00]──tomado──▶[01]──tomado──▶[11]──tomado──▶[11]
Forte NT        Fraco NT      Fraco T        Forte T
 ▲                  │  ▲                  │
 └──────────────────┘  └──────────────────┘
       não tomado             não tomado
```

| Estado | Nome | Previsão |
|---|---|---|
| `00` | Fortemente não tomado | Não tomado |
| `01` | Fracamente não tomado | Não tomado |
| `10` | Fracamente tomado | Tomado |
| `11` | Fortemente tomado | Tomado |

Transições:
- Tomado → avança para direita (máx `11`)
- Não tomado → recua para esquerda (mín `00`)

Taxa de acerto típica: ~85–95% em código real.

## Branch Target Buffer (BTB)

Cache associativo que armazena, para cada instrução de desvio já vista:
- **PC do desvio** (chave)
- **Endereço de destino** previsto
- **Estado do preditor** (bits de histórico)

Fluxo com BTB:
```
1. Fetch instrução no PC
2. Consulta BTB com PC
3. Se hit: busca instrução no endereço previsto imediatamente
4. Quando desvio resolve: verifica previsão
   - Correto → sem penalidade
   - Errado  → flush do pipeline, atualiza BTB
```

Sem BTB, mesmo com previsão correta, o processador precisaria esperar calcular o endereço de destino.

## Penalidade de Erro (Misprediction Penalty)

Depende do número de estágios entre Fetch e resolução do desvio:

| Microarquitetura | Estágios até resolver desvio | Penalidade típica |
|---|---|---|
| Pipeline simples 5 estágios | 2–3 | 2–3 ciclos |
| Core i7 Sandy Bridge | ~15 | 15–20 ciclos |
| Cortex-A9 | ~8 | 8 ciclos |

> [!warning] Impacto em pipelines profundos
> Quanto mais estágios, maior a penalidade por erro. Por isso, processadores modernos investem em preditores sofisticados (perceptron, TAGE) para manter taxa de acerto >98%.

## Previsão com Correlação (2-nível)

Usa histórico de **múltiplos desvios anteriores** para prever o desvio atual. Dois registradores de histórico:
- **BHR** (Branch History Register): shift register com últimos k resultados
- **PHT** (Pattern History Table): tabela de saturating counters indexada por BHR

Aumenta precisão capturando padrões como "if A então geralmente B".

## Ver também

- [[Pipeline]] — hazards de controle que a previsão mitiga
- [[Execução Fora de Ordem]] — previsão é pré-requisito para execução especulativa
- [[Caminho de Dados]] — onde a resolução do desvio acontece (estágio execute)
- [[ARM]] — Cortex-A9 usa previsão dinâmica de 2 bits
