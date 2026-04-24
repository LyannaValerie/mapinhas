---
title: Alocação de Registradores
tags:
  - compiladores
  - back-end
  - registradores
  - ec/cap13
aliases:
  - Register Allocation
  - Alocador de Registradores
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Compiladores]]

# Alocação de Registradores

Mapeia espaço de nomes ilimitado (registradores virtuais do IR) para o conjunto finito de registradores físicos da máquina alvo.  
Insere **spill code** (loads/stores) quando valores não cabem nos registradores disponíveis.

Objetivo: minimizar impacto do spill code (tempo de execução, espaço de código, espaço de dados).

## Problema: Alocação vs Atribuição

| Etapa | O que decide |
|-------|-------------|
| **Alocação** | quais valores vivem em registradores vs memória |
| **Atribuição** | qual registrador físico cada valor alocado recebe |

Alocação sem atribuição → reduz pressão mas não produz código executável.  
Atribuição sem alocação → só possível quando todos os valores cabem.

Problema geral: NP-completo. Para bloco único com tipo único: solução ótima em tempo polinomial.

## Modelos de Memória

**Register-to-register**: IR usa registradores virtuais; loads/stores explícitos apenas em acesso a memória do programa. Compilador virtualiza registradores.

**Memory-to-memory**: IR assume que valores vivem em memória; alocador promove subconjunto para registradores. Código de entrada usa mais memória; alocador substitui loads/stores por uso de registrador.

## Classes de Registradores

Registradores particionados em classes (ex: inteiros, ponto flutuante, branch-target).  
Valor deve ser alocado na classe correta para a operação que o usa.  
Alocador pode precisar inserir cópias entre classes (*class coercion*).

## Alocação Local (Bloco Único)

Opera dentro de um único bloco básico. Live range local = intervalo em código em linha reta.

### Top-Down Local

1. Ordena valores por frequência de uso no bloco
2. Atribui registradores aos mais usados primeiro
3. Reserva alguns registradores para valores que não couberam (usados como temporários)

Vantagem: simples. Desvantagem: valor referenciado apenas no meio do bloco ocupa registrador por todo o bloco.

### Bottom-Up Local (Algoritmo de Best)

Scan do bloco de trás para frente (ou forward com lookahead):
1. Quando valor é necessário e não há registrador livre: **spill** o valor com uso mais distante no futuro (LFU — Longest Next Use)
2. Atribui registrador ao valor necessário
3. Marca registrador como livre quando valor não tem mais usos futuros

Ótimo para bloco único com uma classe de registradores.  
Proposto por Best; base do bottom-up global (Chaitin).

### Além de Blocos Únicos (Local)

Ao final de cada bloco: garantir que valores vivos na saída estejam em memória ou em registrador consistente com os sucessores.  
Join points: unificar estado de registradores de predecessores distintos.

> [!note] Motivação para Alocação Global
> Alocação local trata cada bloco de forma independente.  
> Valores que cruzam fronteiras de bloco são sempre armazenados em memória entre blocos.  
> Alocação global mantém valores em registradores através de múltiplos blocos.

## Relações

- [[Coloração de Grafos — Alocação Global]] — abordagem global dominante
- [[Escalonamento de Instruções]] — interação: lifetimes e pressão de registradores
- [[Forma SSA]] — base para alocação global via SSA
- [[Back End do Compilador]] — posição no pipeline de compilação
- [[ILOC — Linguagem Intermediária]] — formato de entrada do alocador
