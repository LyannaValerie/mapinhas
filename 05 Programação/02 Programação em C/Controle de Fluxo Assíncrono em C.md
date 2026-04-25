---
title: Controle de Fluxo Assíncrono em C
aliases:
  - fluxo assíncrono C
  - sinais e threads em C
  - interrupção em C
tags:
  - computação/programação/c
date: 2026-04-24
source: desconhecida
created_by: "criado pelo Codex"
---

# Controle de Fluxo Assíncrono em C

Controle assíncrono em C ocorre quando a execução deixa de ser uma sequência local previsível: handlers de sinal podem interromper uma instrução, e threads podem intercalar tarefas concorrentes.

## Relações (SPO)

- [[Sinais em C]] — introduzem — interrupção assíncrona via handler
- [[Threads em C]] — introduzem — múltiplos fluxos concorrentes de execução
- [[Atomicidade e Consistência de Memória em C]] — define — comunicação segura entre fluxos concorrentes
- [[Sequenciamento em C]] — é insuficiente para — ordenação entre threads sem sincronização

## Efeitos sobre estado

- Um handler de sinal deve tocar apenas estado seguro para sinal assíncrono.
- `sig_atomic_t`, `atomic_flag` e atomics lock-free reduzem risco de atualização parcial.
- Objetos `_Atomic` impõem propriedades de atualização e ordenação que objetos comuns não têm.
- Se exclusão mútua é necessária, use primitiva de sincronização apropriada, como `mtx_t`.

## Leitura operacional

Código sequencial permite raciocínio por bloco básico. Sinais e threads quebram essa hipótese: estado compartilhado exige contrato explícito de sincronização, atomicidade ou isolamento.

## Ver também

- [[Sinais em C]]
- [[Threads em C]]
- [[Atomicidade e Consistência de Memória em C]]
- [[Consistência de Memória]]
