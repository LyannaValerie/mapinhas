---
title: Programa e Estado da Máquina
aliases:
  - execução como estado
  - estado de máquina
tags:
  - computação/fundamentos
source: Programming from the Ground Up (pgubook), metadados bibliográficos completos ausentes no texto local
created_by: Codex
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Programação]]

# Programa e Estado da Máquina

## Definição

Executar um programa é transformar estado: registradores, memória, pilha, arquivos abertos e posição de execução mudam conforme cada instrução.

## Relações (SPO)
- Instrução → transforma → estado da máquina
- Registrador → mantém → valor temporário
- Memória → mantém → dados e instruções
- Pilha → organiza → chamadas e dados temporários
- Sistema operacional → media → recursos externos

## Implicação

Mesmo linguagens de alto nível escondem esse modelo, não o eliminam. Entender estado da máquina ajuda a explicar chamadas de função, ponteiros, arquivos, alocação e falhas de execução.

## Ver também

- [[Registrador]]
- [[Memória]]
- [[Pilha]]
- [[Modelo de Von Neumann]]
- [[Memória Virtual]]

## Nota de integração

criado pelo Codex
