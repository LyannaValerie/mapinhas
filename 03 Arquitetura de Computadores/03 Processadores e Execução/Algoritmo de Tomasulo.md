---
title: Algoritmo de Tomasulo
aliases:
  - Tomasulo's Algorithm
  - Tomasulo
tags:
  - computação/arquitetura
  - pipeline
date: 2026-04-22
source: desconhecida
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Algoritmo de Tomasulo

Algoritmo de [[Escalonamento Dinâmico]] que usa reservation stations, broadcast de resultados e renaming implícito para reduzir stalls e eliminar hazards WAR e WAW.

## Relações (SPO)
- Algoritmo de Tomasulo → usa → reservation stations
- Algoritmo de Tomasulo → faz → renaming implícito por tags
- Algoritmo de Tomasulo → elimina → WAR e WAW
- Algoritmo de Tomasulo → reduz → stalls por dependência de dados
- [[Execução Fora de Ordem]] → deriva de → ideias de Tomasulo

## Componentes

| Componente | Função |
|---|---|
| Reservation station | mantém operação e dependências pendentes |
| Tag | identifica produtor de um operando |
| Common Data Bus (CDB) | difunde resultado para múltiplos consumidores |
| Load/store buffer | controla operações de memória |

## Funcionamento

1. **Issue**: instrução ocupa reservation station livre.
2. **Rename**: destino passa a ser identificado por uma tag, não pelo registrador arquitetural.
3. **Wait**: operandos ausentes são rastreados por tags.
4. **Execute**: operação inicia quando todas as entradas ficam prontas.
5. **Write result**: resultado é transmitido no CDB e libera dependentes.

## Vantagens sobre scoreboard

- detecção de dependência distribuída
- menos stalls artificiais
- suporte natural a renaming

## Limitações

- alto custo de comparação e broadcast
- escala pior conforme cresce janela de instruções
- precisa ser combinado com ROB para commit preciso em CPUs modernas

## Ver também
- [[Escalonamento Dinâmico]]
- [[Execução Fora de Ordem]]

criado pelo Codex
