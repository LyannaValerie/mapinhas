---
title: Atomicidade e Consistência de Memória em C
aliases:
  - atomics C
  - atomicidade C
  - consistência de memória C
  - happened before C
tags:
  - computação/fundamentos
source: Jens Gustedt, material didático sobre C23
date: 2026-04-23
created_by: Codex
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Programação]]

# Atomicidade e Consistência de Memória em C

## Definição

Modelo que especifica quando acessos concorrentes à memória são indivisíveis e como operações em threads diferentes podem ser ordenadas. É complemento de [[Threads em C]].

## Relações (SPO)
- Operação atômica → evita → disputa de dados naquele objeto
- Relação happened-before → ordena → efeitos visíveis entre threads
- Chamada de sincronização → pode estabelecer → ordem entre threads
- Consistência sequencial → fornece → ordem global simples
- Modelos relaxados → reduzem → garantias de ordenação

---

## Happened-before

Uma operação só pode ser usada como base de raciocínio concorrente quando há relação de ordenação suficiente. Sem ordem, duas threads podem observar efeitos em sequência diferente.

## Consistência sequencial

Modelo mais simples: operações atômicas parecem ocorrer em uma ordem global única compatível com a ordem local de cada thread. É mais fácil de raciocinar, mas pode ter custo maior.

## Modelos relaxados

Modelos relaxados permitem mais reordenação. Devem ser usados apenas quando o protocolo de sincronização foi especificado e testado com rigor.

## Nota de integração

criado pelo Codex

