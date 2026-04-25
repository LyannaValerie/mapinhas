---
title: Tabela de Processos
aliases:
  - process table
  - tabela de processos do kernel
  - process control table
tags:
  - computação/sistemas
source: "Sistemas Operacionais Modernos, cap. 1"
created_by: Codex
date: 2026-04-25
---

%% criado pelo Codex %%

# Tabela de Processos

## Definição

Estrutura mantida pelo [[Kernel]] com os metadados necessários para controlar cada [[Processo]].

## Relações (SPO)

- Tabela de Processos -> contém -> estado de cada [[Processo]]
- Tabela de Processos -> permite -> troca de contexto
- [[Escalonador]] -> consulta -> Tabela de Processos
- [[Sistema de Tempo Compartilhado]] -> depende de -> Tabela de Processos
- Entrada de processo -> referencia -> memória, arquivos abertos e registradores salvos

## Papel técnico

Quando um processo perde a CPU, o kernel precisa preservar informação suficiente para retomá-lo depois. A tabela de processos organiza esse estado e permite que o escalonador escolha qual processo executa em seguida.

## Conteúdo típico

- PID e identidade do processo
- Estado de execução
- Registradores salvos
- Ponteiros para memória do processo
- Arquivos e diretórios associados
- Informações de prioridade e contabilização
