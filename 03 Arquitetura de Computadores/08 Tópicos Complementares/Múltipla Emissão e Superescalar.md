---
title: Múltipla Emissão e Superescalar
aliases:
  - Multiple Issue
  - superescalar
  - issue width
  - emissão múltipla
tags:
  - computação/arquitetura
  - pipeline
  - desempenho
date: 2026-04-22
source: "P&H Cap. 3 §3.7–3.8"
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Múltipla Emissão e Superescalar

Técnica que permite emitir **mais de uma instrução por ciclo**, reduzindo o CPI abaixo de 1 (IPC > 1). Existe em duas formas: estática (compilador decide) e dinâmica (hardware decide).

## Relações (SPO)
- Múltipla Emissão → reduz → CPI efetivo abaixo de 1
- Superescalar → detecta → instruções independentes em hardware
- [[VLIW]] → delega a → compilador a exposição do paralelismo
- [[Especulação Baseada em Hardware]] → amplia → janela de instruções disponíveis
- [[Algoritmo de Tomasulo]] → viabiliza → múltipla emissão com escalonamento dinâmico
- Dependências reais → limitam → IPC efetivo observado

## Formas de Múltipla Emissão

| Tipo | Quem detecta paralelismo | Mecanismo | Exemplo |
|---|---|---|---|
| Estática (VLIW) | Compilador | instrução longa c/ múltiplos slots | Itanium, DSPs |
| Dinâmica (superescalar) | Hardware | detecção run-time de independência | Intel Core, ARM Cortex |
| Dinâmica + especulação | Hardware + preditor de desvio | execução além de branches | Core i7, AMD Zen |

## Superescalar: Funcionamento

Pipeline clássico emite 1 instrução/ciclo. Superescalar mantém **múltiplas unidades funcionais** e emite N instruções por ciclo se:
1. não houver hazard estrutural (recursos livres)
2. não houver dependência entre as instruções do mesmo slot
3. não houver dependência com instruções em voo

```
Ciclo:    1     2     3     4
Slot A:  [I1]  [I3]  [I5]  [I7]
Slot B:  [I2]  [I4]  [I6]  [I8]
```

IPC teórico = largura de emissão. IPC real muito menor por dependências.

## Emissão Dinâmica + Especulação (§3.8)

Combina:
- [[Escalonamento Dinâmico]] (Tomasulo + ROB)
- múltipla emissão por ciclo
- [[Especulação Baseada em Hardware]]

Resultado: o hardware executa instruções especulativamente de múltiplos caminhos potenciais, preenchendo slots ociosos. Commita em ordem ao confirmar previsão.

## Gap entre Teoria e Prática

| Fator | Efeito |
|---|---|
| Dependências RAW | travamento de slot(s) |
| Branches não resolvidos | limita janela especulativa |
| Hazards estruturais | reduz emissão efetiva |
| Latência de memória | esvazia pipeline mesmo com ILP |
| Dependências de memória | requer análise dinâmica de aliasing |

Processadores de emissão 4-wide (IPC teórico = 4) raramente superam IPC médio de 2–3 em código real.

## Complexidade de Hardware

Crescimento superlinear de custo com largura de emissão:
- comparadores de hazard: O(N²) para N slots
- bancos de registradores: N portas de leitura por unidade funcional
- lógica de bypass: N × M conexões

Por isso, a largura prática de superescalar em 2024 fica em 4–8 instruções/ciclo.

## Ver também
- [[VLIW]] — alternativa estática ao superescalar
- [[Paralelismo em Nível de Instrução]] — motivação e limites do ILP
- [[Escalonamento Dinâmico]] — mecanismo de reordenação dinâmica
- [[Especulação Baseada em Hardware]] — amplia ILP além de branches
- [[Algoritmo de Tomasulo]] — implementação de referência
