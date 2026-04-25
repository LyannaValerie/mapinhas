---
title: Inspeção em Tempo de Execução
aliases:
  - runtime inspection
  - debug em tempo de execução
  - inspeção dinâmica
tags:
  - computação/sistemas
source: "Tu, Do Hoang. Operating Systems: From 0 to 1"
created_by: Codex
---

# Inspeção em Tempo de Execução

%% criado pelo Codex %%

## Definição

Inspeção em tempo de execução é análise de um programa enquanto ele está carregado ou executando. Um depurador permite iniciar, parar, examinar e alterar estado do programa.

## Depurador

Um breakpoint marca ponto em que o programa para para inspeção sem encerrar. Com `gdb`, é possível iniciar o programa, parar sob condições específicas, examinar estado após parada e alterar valores para testar hipóteses de correção.

## Relações

- Complementa inspeção estática com [[Objdump]] e [[Readelf]].
- Depende de símbolos e informações de debug geradas pelo [[Sistema de Compilação]].
- É técnica central para depurar [[Sistema Operacional|kernel]] e código baixo nível.
