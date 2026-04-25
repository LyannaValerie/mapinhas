---
title: Sistema Operacional
aliases:
  - operating system
  - SO
  - OS
tags:
  - computação/sistemas
source: "Sistemas Operacionais Modernos, cap. 1"
created_by: Codex
date: 2026-04-25
---

%% criado pelo Codex %%

# Sistema Operacional

## Definição

Programa de sistema que controla os recursos do computador e oferece abstrações para programas de aplicação. O sistema operacional fica entre o hardware e os programas de usuário.

## Duas visões centrais

- [[Sistema Operacional como Máquina Estendida]]: esconde detalhes de hardware e expõe abstrações mais simples, como arquivos, processos e chamadas de sistema.
- [[Sistema Operacional como Gerenciador de Recursos]]: decide como CPU, memória, dispositivos e arquivos são compartilhados entre programas concorrentes.

## Relações (SPO)

- Sistema Operacional -> controla -> [[Processador]]
- Sistema Operacional -> gerencia -> [[Memória]]
- Sistema Operacional -> expõe -> [[Chamada do Sistema]]
- Sistema Operacional -> cria abstração de -> [[Processo]]
- Sistema Operacional -> organiza -> [[Sistema de Arquivos em Sistemas Operacionais]]
- [[Kernel]] -> implementa -> núcleo privilegiado do sistema operacional
- [[Modo Kernel e Modo Usuário]] -> separa -> código privilegiado e código de aplicação

## Papel técnico

Sem sistema operacional, cada programa precisaria lidar diretamente com controladores de disco, interrupções, temporizadores, alocação de memória e detalhes de dispositivos. O SO padroniza essas operações e aplica proteção entre programas.

## Ver também

- [[Chamada do Sistema]]
- [[Processo]]
- [[Kernel]]
- [[Shell]]
