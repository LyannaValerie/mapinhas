---
title: Entrada e Saída em Sistemas Operacionais
aliases:
  - E/S em sistemas operacionais
  - I/O em sistemas operacionais
  - gerenciamento de E/S
tags:
  - computação/sistemas
source: "Sistemas Operacionais Modernos, cap. 5"
created_by: Codex
---

# Entrada e Saída em Sistemas Operacionais

Conteúdo criado pelo Codex.

## Definição

**Entrada e saída em sistemas operacionais** é o subsistema que controla dispositivos, emite comandos, recebe [[Interrupção|interrupções]], trata erros e oferece uma interface uniforme para programas.

## Papel técnico

O [[Sistema Operacional como Gerenciador de Recursos]] não expõe diretamente os detalhes físicos de cada dispositivo. Ele organiza a E/S em camadas para separar:

- controle específico de hardware
- política de alocação de recursos
- tratamento de erros
- bufferização
- interface de chamadas de sistema

## Relações (SPO)

- Entrada e Saída em Sistemas Operacionais -> controla -> [[Controlador de Dispositivo]]
- Entrada e Saída em Sistemas Operacionais -> usa -> [[Driver]]
- Entrada e Saída em Sistemas Operacionais -> recebe -> [[Interrupção]]
- Entrada e Saída em Sistemas Operacionais -> pode usar -> [[DMA]]
- Entrada e Saída em Sistemas Operacionais -> organiza -> [[Camadas do Software de E-S|Camadas do Software de E/S]]
