---
title: Sistema de Tempo Compartilhado
aliases:
  - time-sharing
  - tempo compartilhado
  - sistema interativo multiusuário
tags:
  - computação/sistemas
source: "Sistemas Operacionais Modernos, cap. 1"
created_by: Codex
date: 2026-04-25
---

%% criado pelo Codex %%

# Sistema de Tempo Compartilhado

## Definição

Variante de multiprogramação que dá a múltiplos usuários simultâneos a ilusão de uma máquina interativa dedicada. A CPU é dividida em quanta de tempo; quando o quantum de um processo expira, o sistema salva o contexto e escaloneia o próximo.

## Origem histórica

O conceito surgiu na terceira geração (1965–1980) como resposta à limitação do [[Sistema em Lote]] para uso interativo (Tanenbaum §1.2.3):

- **CTSS** (Compatible Time-Sharing System) — MIT, 1961, desenvolvido por Fernando Corbató. Primeiro sistema de tempo compartilhado amplamente reconhecido; rodava no IBM 7090.
- **MULTICS** — sucessor do CTSS, desenvolvido por MIT, Bell Labs e General Electric. Muito ambicioso para a época; introduziu conceitos como sistemas de arquivos hierárquicos, proteção por anéis e memória virtual. Escrito em PL/I.
- **UNIX** — derivado de experiências com o MULTICS; mais simples e portável, escrito em C.

## Mecanismo

1. Relógio de hardware gera interrupção periódica (e.g., a cada 10–100 ms)
2. O kernel salva o contexto do processo atual na [[Tabela de Processos]]
3. O [[Escalonador]] seleciona o próximo processo
4. O kernel restaura o contexto do novo processo
5. A CPU retoma execução do novo processo

O intervalo entre interrupções é o **quantum** (ou time slice). Quanta curtos → maior responsividade; quanta longos → menor overhead de troca de contexto.

## Multiprogramação vs tempo compartilhado

| Aspecto | Multiprogramação pura | Tempo compartilhado |
|---|---|---|
| Objetivo | Maximizar uso de CPU | Minimizar tempo de resposta |
| Troca de processo | Quando processo bloqueia em E/S | Também por expiração de quantum |
| Usuários | Pode ser um único | Múltiplos simultâneos |

## Relações (SPO)

- Sistema de tempo compartilhado → depende de → [[Escalonamento de CPU]]
- Sistema de tempo compartilhado → exige → [[Tabela de Processos]]
- Sistema de tempo compartilhado → usa → interrupção de relógio para troca de quantum
- CTSS → implementa → tempo compartilhado no IBM 7090 (MIT, 1961)
- MULTICS → generaliza → CTSS com proteção por anéis e sistemas de arquivos hierárquicos
- UNIX → deriva de → experiências com MULTICS
- [[Multitarefa]] → implementa → compartilhamento temporal da CPU
- [[Shell]] → fornece → interface interativa para cada usuário
- Sistema de tempo compartilhado → contrasta com → [[Sistema em Lote]]
