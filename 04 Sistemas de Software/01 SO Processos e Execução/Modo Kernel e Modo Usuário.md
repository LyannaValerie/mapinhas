---
title: Modo Kernel e Modo Usuário
aliases:
  - kernel mode
  - user mode
  - modo supervisor
  - modo privilegiado
  - modo usuário
tags:
  - computação/sistemas
source: "Sistemas Operacionais Modernos, cap. 1"
created_by: Codex
date: 2026-04-25
---

%% criado pelo Codex %%

# Modo Kernel e Modo Usuário

## Definição

Separação de privilégio implementada em hardware entre código do [[Kernel]] e código de aplicação. Um bit na palavra de status do processador (PSW — Program Status Word) indica o modo atual. Em modo kernel (também chamado modo supervisor ou modo privilegiado), a CPU permite qualquer instrução; em modo usuário, instruções privilegiadas causam armadilha (trap).

## Instruções privilegiadas (modo kernel apenas)

- Configurar registradores de controle da MMU (tabelas de página)
- Programar controladores de E/S e DMA
- Habilitar/desabilitar interrupções
- Acessar portas de E/S diretamente
- Mudar o modo de execução da CPU

## Mecanismo de transição: TRAP

Quando um programa em modo usuário precisa de um serviço do SO (Tanenbaum §1.6, Figura 1.17):

1. Programa coloca parâmetros em registradores ou na pilha
2. Programa emite instrução de TRAP (em x86: `syscall` ou `int 0x80`)
3. Hardware troca o bit de modo: usuário → kernel
4. Hardware salta para o endereço do vetor de interrupção do kernel
5. Kernel identifica qual [[Chamada do Sistema]] foi solicitada (número em registrador)
6. Kernel indexa tabela de syscalls e executa a rotina de serviço
7. Rotina executa em modo kernel com acesso total
8. Kernel copia resultado para espaço de usuário
9. Instrução `reti`/`sysret` restaura modo usuário e retorna ao programa

## Papel técnico

A distinção impede que aplicações modifiquem registradores de controle, acessem dispositivos diretamente ou alterem estruturas internas do SO. Sustenta isolamento, segurança e multiprogramação. Um processo defeituoso não pode corromper o estado do kernel ou de outros processos.

## Rings (x86)

A arquitetura x86 oferece quatro anéis de proteção (rings 0–3). Sistemas operacionais como Linux e Windows usam apenas dois: ring 0 (kernel) e ring 3 (usuário). Rings 1 e 2 existem na arquitetura mas raramente são usados.

## Relações (SPO)

- Modo kernel → executa → código do [[Sistema Operacional]]
- Modo usuário → executa → programas de aplicação
- [[Chamada do Sistema]] → provoca → transição usuário → kernel via TRAP
- Instrução privilegiada → exige → modo kernel
- Bit de modo na PSW → determina → modo atual da CPU
- Separação de modos → protege → recursos compartilhados
- TRAP → entrega → controle ao kernel com número de syscall
