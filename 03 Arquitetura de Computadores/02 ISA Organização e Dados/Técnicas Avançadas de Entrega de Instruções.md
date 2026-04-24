---
title: Técnicas Avançadas de Entrega de Instruções
aliases:
  - Advanced Instruction Delivery
  - trace cache
  - instruction fetch optimization
  - return address stack
tags:
  - computação/arquitetura
  - pipeline
  - desempenho
date: 2026-04-22
source: "P&H Cap. 3 §3.9"
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Técnicas Avançadas de Entrega de Instruções

Em processadores superescalares largos, o gargalo migra do backend (execução) para o **frontend** (busca e decodificação). A taxa de entrega de instruções deve ser ao menos igual à largura de emissão para não desperdiçar capacidade de execução.

## Relações (SPO)
- Frontend de instrução → deve sustentar → largura de emissão do superescalar
- Trace cache → armazena → sequências de instruções já decodificadas
- BTB → provê → endereço de destino de desvio antes de calcular
- Return Address Stack → prediz → endereço de retorno de chamada de função
- Instruction prefetch → reduz → stalls de I-cache miss

## Problemas de Entrega de Instruções

| Problema | Causa | Impacto |
|---|---|---|
| Branch misprediction | previsão errada → flush | bolha de 10–20 ciclos |
| I-cache miss | bloco de instrução ausente | stall até DRAM |
| Decodificação lenta | instruções de tamanho variável (x86) | limita throughput |
| Branches densos | reduzem fluxo sequencial contínuo | janela útil reduzida |

## Branch Target Buffer (BTB)

Cache que guarda, para cada branch já visto:
- PC do branch (chave)
- endereço de destino previsto
- estado do preditor (bits de saturação)

Permite iniciar fetch do próximo bloco **sem calcular o alvo** — o endereço já está cacheado. Hit em BTB elimina a bolha de 1 ciclo de calcular destino.

## Return Address Stack (RAS)

Pilha em hardware para prever endereço de retorno de funções:
- **CALL**: empilha PC+4 no RAS
- **RETURN**: desempilha e usa como endereço de fetch

Fundamental porque preditores genéricos falham em retorno: o alvo muda a cada chamada diferente. RAS de 8–16 entradas acerta ~95% dos retornos.

## Trace Cache

Cache que armazena **sequências de instruções já decodificadas** (traces) — não blocos alinhados de memória. Um trace atravessa branches:

```
Trace: [I1][I2][branch→tomado][I10][I11][I12]
```

Vantagens:
- entrega instruções pós-branch sem bolha de fetch
- armazena instruções decodificadas → evita decodificar novamente (especialmente útil para x86)
- aumenta largura efetiva de entrega

Desvantagem: mesma instrução pode aparecer em múltiplos traces → maior overhead de espaço.

## Instruction Prefetch

Busca antecipada de blocos de instrução antes de serem necessários. Combinado com preditor de desvio, o prefetch inicia fetch do caminho previsto no mesmo ciclo que a previsão é feita.

## Ver também
- [[Previsão de Desvio]] — predição que guia o fetch especulativo
- [[Especulação Baseada em Hardware]] — executa instruções fetchadas especulativamente
- [[Múltipla Emissão e Superescalar]] — frontend deve sustentar a largura de emissão
- [[Hierarquia de Memória]] — I-cache miss como causa de stall de entrega
