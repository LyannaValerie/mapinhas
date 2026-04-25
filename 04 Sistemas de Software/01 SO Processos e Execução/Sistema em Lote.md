---
title: Sistema em Lote
aliases:
  - batch system
  - processamento em lote
  - sistema batch
tags:
  - computação/sistemas
source: "Sistemas Operacionais Modernos, cap. 1"
created_by: Codex
date: 2026-04-25
---

%% criado pelo Codex %%

# Sistema em Lote

## Definição

Modelo de execução da segunda geração de computadores (1955–1965) em que trabalhos são agrupados e processados sequencialmente sem interação direta do usuário. Surgiu para reduzir o tempo ocioso de mainframes caros.

## Mecanismo operacional (Tanenbaum §1.2.2)

O fluxo típico numa instalação IBM dos anos 1960:

1. Programadores entregavam cartões perfurados na sala de entradas
2. Um operador coletava um lote e o lia em fita magnética com um **IBM 1401** (máquina pequena, boa para E/S, ruim para cálculo)
3. A fita era levada à sala do **IBM 7094** (máquina cara, focada em cálculo)
4. O 7094 processava os jobs sequencialmente, gravando saída em outra fita
5. A fita de saída voltava ao 1401 para impressão off-line

A separação de máquinas (1401 vs 7094) permitia sobreposição de E/S e cálculo — o 7094 nunca precisava esperar pela impressora.

## Estrutura de um job FMS

```
$JOB, 10, 7710802, MARVIN TANENBAUM   ← tempo máximo, conta, programador
$FORTRAN                               ← carregar compilador FORTRAN
  ... código-fonte ...
$LOAD                                  ← carregar o programa compilado
$RUN                                   ← executar
  ... dados de entrada ...
$END                                   ← fim do job
```

Os cartões de controle (`$JOB`, `$FORTRAN`, etc.) eram os precursores das linguagens de controle de jobs (JCL) e shells modernos.

## Papel técnico

Objetivo: maximizar utilização de hardware caro. O sistema operacional encadeava jobs automaticamente, sem intervenção do operador entre execuções. O custo era alta latência: o resultado só chegava ao programador horas depois.

## Spooling

Uma melhoria posterior foi o **spooling** (Simultaneous Peripheral Operations On-Line): a saída de um job era gravada em disco enquanto outro job executava, depois impressa assincronamente. Isso desacoplou a CPU da impressora.

## Limitação

Ruim para depuração: um erro descoberto horas após a submissão obrigava resubmissão do job. O [[Sistema de Tempo Compartilhado]] surgiu como resposta a essa limitação.

## Relações (SPO)

- Sistema em lote → agrupa → jobs para processamento sequencial
- Sistema em lote → reduz → intervenção manual do operador
- Sistema em lote → maximiza → utilização de hardware caro
- IBM 1401 → realizava → E/S (leitura de cartões, impressão)
- IBM 7094 → realizava → cálculo numérico
- [[Spooling]] → melhora → fluxo de E/S desacoplando CPU de dispositivos lentos
- Sistema em lote → precede → [[Sistema de Tempo Compartilhado]]
- Job → contém → cartões de controle, programa e dados
