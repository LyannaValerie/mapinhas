---
title: Teoria de Filas para IO
aliases:
  - queuing theory
  - M/M/1
  - M/M/m
  - Little's Law
  - lei de Little
  - tempo de fila
tags:
  - computação/arquitetura
  - sistemas
  - desempenho
date: 2026-04-22
source: "P&H Apêndice D §D.5"
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Teoria de Filas para IO

Modelo matemático para prever desempenho de sistemas I/O sob carga. Permite estimar latência, comprimento de fila e utilização a partir de taxa de chegada e tempo de serviço.

## Relações (SPO)
- Utilização → é → taxa de chegada × tempo de serviço
- Tempo na fila → cresce → não linearmente com utilização
- M/M/1 → modela → 1 servidor com chegadas Poisson e serviço exponencial
- Lei de Little → relaciona → comprimento médio de fila, taxa de chegada e tempo de espera
- [[Confiabilidade e Disponibilidade]] → complementa → análise de I/O em sistemas reais

## Modelo Básico

```
Chegadas ──▶ [Fila] ──▶ [Servidor] ──▶ Saídas
```

| Parâmetro | Símbolo | Definição |
|---|---|---|
| Taxa de chegada | λ | requisições/segundo |
| Tempo de serviço | Tserver | segundos/requisição |
| Utilização | ρ | λ × Tserver (adimensional, 0–1) |
| Número de servidores | m | paralelismo do sistema |

## Lei de Little

$$N = \lambda \cdot T$$

onde N = número médio de tarefas no sistema, T = tempo médio de residência (espera + serviço).

Independe da distribuição de chegadas ou serviço.

## Fila M/M/1

Chegadas Poisson (memoryless), serviço exponencial, 1 servidor:

$$T_\text{fila} = \frac{\rho}{(1 - \rho)} \cdot T_\text{server}$$

$$T_\text{sistema} = T_\text{server} + T_\text{fila} = \frac{T_\text{server}}{1 - \rho}$$

> [!warning] Comportamento não-linear
> Para ρ = 0,5 → Tsistema = 2 × Tserver.
> Para ρ = 0,9 → Tsistema = 10 × Tserver.
> Para ρ → 1 → Tsistema → ∞.
> Nunca projetar sistema com utilização acima de 70–80% sem margem para bursts.

## Fila M/M/m

m servidores idênticos, fila compartilhada:

$$\rho_\text{por servidor} = \frac{\lambda \cdot T_\text{server}}{m}$$

Reduz drasticamente o tempo de fila vs. m sistemas M/M/1 independentes. Exemplo do livro: 2 discos lentos compartilhando fila chegam a desempenho quase equivalente a 1 disco rápido com throughput 2×.

## Aplicação a Disco

Exemplo: disco com:
- Tempo de serviço = 20 ms (seek + rotação + transferência)
- Taxa de chegada = 40 req/s

$$\rho = 40 \times 0,02 = 0,8$$

$$T_\text{fila} = \frac{0,8}{0,2} \times 20 = 80 \text{ ms}$$

$$T_\text{sistema} = 100 \text{ ms}$$

Ao adicionar segundo disco (M/M/2) com mesma carga:
- ρ por disco = 0,4 → Tsistema cai dramaticamente

## Disciplina de Fila

| Disciplina | Descrição |
|---|---|
| FIFO | mais simples; sem reordenação |
| SSTF | Shortest Seek Time First; otimiza seek, mas causa starvation |
| SCAN (elevator) | varre disco em uma direção; equilibrado |
| C-SCAN | varre em uma direção, retorna sem servir; mais uniforme |

## Ver também
- [[Confiabilidade e Disponibilidade]] — métricas complementares para sistemas I/O
- [[Hierarquia de Memória]] — latência de acesso a disco no contexto da hierarquia
- [[WSC — Warehouse Scale Computer]] — fila e utilização em datacenters
