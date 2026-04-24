---
title: Escalonamento de Instruções
tags:
  - compiladores
  - back-end
  - otimização
  - ec/cap12
aliases:
  - Instruction Scheduling
  - Escalonador de Instruções
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Compiladores]]

# Escalonamento de Instruções

Reordena operações de um bloco/procedimento para reduzir tempo de execução.  
Entrada: lista parcialmente ordenada de operações em assembly do alvo.  
Saída: mesma lista reordenada (mesmo conjunto de ops + nops se necessário).

O escalonador assume:
- código já otimizado (não reescreve semântica)
- alocação de registradores fixa (pode renomear, mas não mudar alocação)

## Objetivos

1. Preservar semântica do código
2. Minimizar stalls e nops
3. Não aumentar pressão de registradores além do necessário

## Conceitos Centrais

**Latência**: ciclos que uma operação leva para produzir resultado.  
Inteiros: add/sub = 1, mul = vários, div = 12–18.  
Loads: 1–5 ciclos (cache L1) até centenas (memória principal).

**ILP** (*Instruction Level Parallelism*): operações independentes que podem executar concorrentemente.

**Superescalar**: processador que emite múltiplas operações a múltiplas unidades funcionais por ciclo. Detecta ILP em hardware.

**VLIW** (*Very Long Instruction Word*): instrução de tamanho fixo com uma operação por unidade funcional por ciclo. Compilador é responsável pelo empacotamento. Slots ociosos = NOPs.

**Packed VLIW**: instrução de tamanho variável; evita NOPs explícitos.

## Grafo de Dependência $D$

- Nó por operação
- Aresta $i \to j$ se $j$ usa resultado de $i$; anotada com latência de $i$
- Folhas de $D$ = operações sem dependentes (prontas para emissão primeiro)
- Antidependências (WAR, WAW): eliminadas por renomeação antes do escalonamento

**Caminho crítico**: maior soma de latências em qualquer caminho em $D$.  
Limite inferior do tamanho do schedule.

## Tipos de Dependência

| Tipo | Nome | Situação |
|------|------|----------|
| RAW | true dependence | $j$ lê valor produzido por $i$ |
| WAR | antidependence | $j$ escreve em local lido por $i$ anterior |
| WAW | output dependence | $i$ e $j$ escrevem no mesmo local |

RAW impõe restrição real. WAR/WAW são eliminadas por renomeação.

## Métricas de Qualidade

- **Comprimento do schedule**: ciclo da última operação emitida
- **Ciclos por instrução (CPI)**: média de ciclos por operação
- **Instruções por ciclo (IPC)**: inverso de CPI
- Benchmark de tempo real: métrica mais completa de qualidade de geração de código

## Dificuldades

- Latências variáveis (loads dependem do nível de cache atingido)
- Múltiplas unidades funcionais com restrições distintas
- Escalonamento NP-completo no caso geral → heurísticas
- Interação com alocação de registradores: mover ops pode aumentar lifetimes

> [!note] Relação com outros passes
> O escalonador opera após o otimizador e após a alocação de registradores.  
> Pode renomear registradores para eliminar antidependências, mas não altera quais valores vivem em quais registradores.

## Relações

- [[List Scheduling — Escalonamento Local]] — algoritmo dominante
- [[Escalonamento Regional e Software Pipelining]] — extensões para múltiplos blocos
- [[Alocação de Registradores]] — interação: lifetimes vs pressão
- [[IR Linear — Código de Três Endereços]] — formato de entrada do escalonador
- [[Paralelismo em Nível de Instrução]] — conceito de ILP em hardware
- [[Múltipla Emissão e Superescalar]] — contexto de hardware alvo
