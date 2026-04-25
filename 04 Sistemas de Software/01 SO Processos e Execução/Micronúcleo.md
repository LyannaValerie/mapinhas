---
title: Micronúcleo
aliases:
  - microkernel
  - micronucleo
tags:
  - computação/sistemas
source: "Sistemas Operacionais Modernos, cap. 1"
created_by: Codex
date: 2026-04-25
---

%% criado pelo Codex %%

# Micronúcleo

## Definição

Estrutura de sistema operacional que mantém no [[Modo Kernel e Modo Usuário|modo núcleo]] apenas mecanismos mínimos — tipicamente IPC, escalonamento básico e gerenciamento de memória — deslocando todos os demais serviços para processos em espaço de usuário.

## Mecanismo

O micronúcleo expõe apenas primitivas de baixo nível. Serviços de sistema operacional rodam como processos de usuário chamados **servidores**. Quando uma aplicação precisa de um serviço (e.g., abrir arquivo), ela envia uma mensagem ao servidor do sistema de arquivos; o servidor processa e responde. O núcleo apenas transporta as mensagens.

## Serviços fora do kernel

| Serviço | Localização |
|---|---|
| Sistema de arquivos | Processo servidor em espaço de usuário |
| Driver de dispositivo | Processo servidor em espaço de usuário |
| Servidor de rede | Processo servidor em espaço de usuário |
| Gerenciamento de memória virtual | Pode ser movido para fora do núcleo |

## Exemplos históricos e atuais

- **Mach** (Carnegie Mellon University, ACETTA et al., 1986) — base do núcleo do macOS/OS X
- **MINIX 3** (Tanenbaum) — foco em confiabilidade; detecta e substitui módulos defeituosos em tempo de execução sem reinicializar
- **L4** (LIEDTKE, 1993–1996) — micronúcleo de segunda geração otimizado para IPC eficiente
- Dominante em sistemas embarcados e de tempo real

## Trade-off

| Aspecto | Benefício | Custo |
|---|---|---|
| Isolamento | Servidor defeituoso não derruba o sistema | Custo de troca de contexto por mensagem |
| Modularidade | Componentes substituíveis em tempo de execução | Mais chamadas para operações simples |
| Segurança | Menos código privilegiado = menor superfície de ataque | Overhead de IPC para serviços frequentes |

## Relações (SPO)

- Micronúcleo → mantém no modo núcleo → IPC, escalonamento e gerenciamento básico de memória
- Micronúcleo → move → serviços para espaço de usuário
- Micronúcleo → usa → troca de mensagens como mecanismo central
- Micronúcleo → contrasta com → [[Estrutura Monolítica de Sistema Operacional]]
- Micronúcleo → favorece → [[Modelo Cliente-Servidor em Sistemas Operacionais]]
- Mach → implementa → micronúcleo e é base do → macOS
- MINIX 3 → implementa → micronúcleo com substituição de módulos em execução
- [[Driver]] → executa como → servidor de usuário em micronúcleos

