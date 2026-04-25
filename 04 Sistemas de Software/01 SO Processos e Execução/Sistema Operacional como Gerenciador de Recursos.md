---
title: Sistema Operacional como Gerenciador de Recursos
aliases:
  - SO como gerenciador de recursos
  - resource manager
tags:
  - computação/sistemas
source: "Sistemas Operacionais Modernos, cap. 1"
created_by: Codex
date: 2026-04-25
---

%% criado pelo Codex %%

# Sistema Operacional como Gerenciador de Recursos

## Definição

Uma das duas visões centrais do [[Sistema Operacional]]: o SO como componente que controla quais programas usam quais recursos, concede requisições, contabiliza o uso e medeia conflitos entre programas e usuários concorrentes.

Contrasta com a visão de [[Sistema Operacional como Máquina Estendida]], que foca nas abstrações oferecidas, não no controle de acesso.

## Dois tipos de multiplexação

O gerenciamento de recursos envolve dois modos de compartilhamento (Tanenbaum §1.1.2):

| Tipo | Mecanismo | Exemplos |
|---|---|---|
| **Multiplexação no tempo** | Programas se revezam usando o mesmo recurso | CPU (escalonamento), impressora (spooling) |
| **Multiplexação no espaço** | Cada programa recebe uma fração física do recurso | Memória principal (particionada), disco (blocos alocados por usuário) |

## Recursos gerenciados

| Recurso | Mecanismo de controle |
|---|---|
| CPU | [[Escalonamento de CPU]] — quem executa e por quanto tempo |
| Memória | Alocação, isolamento de espaços de endereçamento |
| Disco | Alocação de blocos, sistema de arquivos |
| Dispositivos de E/S | Fila de acesso, drivers, controle de conflitos |

## Papel técnico

A visão de gerenciador de recursos prevalece quando há múltiplos usuários ou processos concorrentes. O SO precisa:
1. Manter registro de quem está usando cada recurso
2. Conceder requisições de forma justa e segura
3. Impedir interferência entre programas (isolamento)
4. Recuperar recursos quando processos terminam

## Relações (SPO)

- Sistema Operacional como Gerenciador de Recursos → controla → [[Processador]]
- Sistema Operacional como Gerenciador de Recursos → controla → [[Memória]]
- Sistema Operacional como Gerenciador de Recursos → controla → dispositivos de E/S e disco
- Sistema Operacional como Gerenciador de Recursos → implementa → isolamento entre [[Processo|processos]]
- Sistema Operacional como Gerenciador de Recursos → usa → multiplexação no tempo e no espaço
- [[Escalonamento de CPU]] → implementa → multiplexação temporal da CPU
- [[Sistema Operacional como Máquina Estendida]] → visão complementar → mesma entidade, perspectiva diferente

