---
title: Escalonamento Dinâmico
aliases:
  - Dynamic Scheduling
  - scoreboard
  - CDC 6600 scoreboard
tags:
  - computação/arquitetura
  - pipeline
  - desempenho
date: 2026-04-23
source: "P&H CA:AQA Cap. 3 §3.4–3.5"
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Escalonamento Dinâmico

Técnica em que o hardware decide em tempo de execução quando emitir, aguardar ou executar instruções. Permite execução fora de ordem preservando semântica do programa.

## Relações (SPO)
- Escalonamento Dinâmico → reordena → execução de instruções
- Escalonamento Dinâmico → preserva → semântica do programa
- Scoreboard → implementa → escalonamento dinâmico **sem** renaming (CDC 6600)
- [[Algoritmo de Tomasulo]] → implementa → escalonamento dinâmico **com** renaming e CDB
- [[Execução Fora de Ordem]] → usa → escalonamento dinâmico

## Motivação

Pipeline clássico emite e completa instruções em ordem de programa. Quando instrução A trava (RAW, latência longa), instrução B independente poderia executar — mas pipeline em ordem a bloqueia desnecessariamente.

Escalonamento dinâmico detecta esse paralelismo em tempo de execução, permitindo que B avance enquanto A aguarda.

## Scoreboard (CDC 6600, §3.4)

Primeiro mecanismo de escalonamento dinâmico — introduzido no CDC 6600 (1964). **Sem renaming**: WAR e WAW resolvidos por stall, não por renaming.

### 4 Estágios

| Estágio | Condição de entrada | O que faz |
|---|---|---|
| **Issue** | UF livre E sem WAW | Emite instrução à unidade funcional; atualiza scoreboard |
| **Read Operands** | Sem RAW (UFs produtoras concluídas) | Lê registradores fonte |
| **Execute** | — | Executa na unidade funcional |
| **Write Result** | Sem WAR (leitores anteriores concluíram leitura) | Escreve resultado no registrador destino |

> [!note] Issue é em ordem; Execute e Write Result são fora de ordem.

### Estruturas de controle do Scoreboard

1. **Instruction status**: em qual dos 4 estágios está cada instrução em voo
2. **Functional unit status** (por UF):
   - `busy`: ocupada?
   - `Op`: operação em execução
   - `Fi, Fj, Fk`: registrador destino e fontes
   - `Qj, Qk`: UF que produzirá Fj / Fk (se não disponível)
   - `Rj, Rk`: flags indicando se Fj / Fk já foram lidos
3. **Register result status**: qual UF irá escrever em cada registrador (ou vazio se nenhuma)

### Hazards no Scoreboard

| Hazard | Como detectado | Como tratado |
|---|---|---|
| RAW | Qj ou Qk não vazio | stall em Read Operands |
| WAW | UF diferente vai escrever no mesmo Fi | stall no Issue |
| WAR | leitores ainda não leram Fj/Fk | stall no Write Result |
| Estrutural | UF ocupada | stall no Issue |

## Comparação: Scoreboard vs. Tomasulo

| Aspecto | Scoreboard | [[Algoritmo de Tomasulo]] |
|---|---|---|
| Renaming | Não — WAR/WAW causam stall | Sim — elimina WAR/WAW |
| Forwarding | Não | Sim (CDB) |
| Emissão | Em ordem | Em ordem |
| Execução | Fora de ordem | Fora de ordem |
| Commit | Fora de ordem | Fora de ordem (sem ROB) |
| Especulação | Não | Com ROB: sim |
| Centralização | Controle central (scoreboard) | Distribuído (reservation stations) |

## Vantagens do Escalonamento Dinâmico

- mesmo binário adapta-se a diferentes microarquiteturas
- esconde latências variáveis de unidades funcionais
- esconde latências de cache miss (parcialmente)
- permite aproveitar ILP que compilador não consegue expor estaticamente

## Ver também
- [[Algoritmo de Tomasulo]] — escalonamento dinâmico com renaming implícito
- [[Especulação Baseada em Hardware]] — adiciona ROB ao Tomasulo para commit em ordem
- [[Execução Fora de Ordem]] — resultado de empregar escalonamento dinâmico
- [[Paralelismo em Nível de Instrução]] — quantidade de ILP que o escalonamento pode explorar
- [[Múltipla Emissão e Superescalar]] — combina escalonamento dinâmico com N instruções/ciclo
