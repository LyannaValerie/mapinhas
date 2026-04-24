---
title: Especulação Baseada em Hardware
aliases:
  - Hardware-Based Speculation
  - execução especulativa por hardware
  - ROB
  - Reorder Buffer
  - buffer de reordenamento
tags:
  - computação/arquitetura
  - pipeline
  - desempenho
date: 2026-04-23
source: "P&H CA:AQA Cap. 3 §3.6"
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Especulação Baseada em Hardware

Técnica em que o processador executa instruções antes de confirmar se o caminho de controle era válido. Estende o [[Algoritmo de Tomasulo]] adicionando o **ROB (Reorder Buffer)** para separar execução especulativa do estado arquitetural.

## Relações (SPO)
- Especulação Baseada em Hardware → depende de → [[Previsão de Desvio]]
- Especulação Baseada em Hardware → estende → [[Algoritmo de Tomasulo]]
- Especulação Baseada em Hardware → amplia → [[Paralelismo em Nível de Instrução]]
- ROB → separa → estado especulativo do estado arquitetural
- ROB → garante → commit em ordem (interrupções precisas)
- Misspeculation → causa → flush do ROB + descarte de trabalho especulativo

## Motivação

Tomasulo executa fora de ordem mas não distingue resultado confirmado de especulativo. Sem ROB:
- Interrupção no meio de execução especulativa → estado arquitetural corrompido
- Branch errado já atualizou registradores → rollback impossível

ROB resolve: resultados especulativos ficam no ROB; só passam ao arquivo de registradores no **commit** (em ordem).

## ROB — Reorder Buffer

Buffer circular FIFO com uma entrada por instrução em voo. Cada entrada tem:

| Campo | Conteúdo |
|---|---|
| **Instruction type** | branch / store / register operation |
| **Destination** | número do registrador destino (ou endereço de memória) |
| **Value** | resultado especulativo calculado |
| **Ready** | indica se execução completou |

Referência a uma entrada do ROB serve como **tag** de renaming — substitui o registrador arquitetural enquanto a instrução está especulativa.

## 4 Estágios com ROB

### 1. Issue (despacho)
- Instrução retirada do buffer de decodificação
- Alocada em **reservation station** (RS) livre E em **ROB** livre
- Operandos obtidos: do arquivo de registradores, de outra RS, ou de entrada do ROB (forwarding)
- Se RS ou ROB cheios: **stall** (hazard estrutural)

### 2. Execute
- Quando todos os operandos da RS estão prontos: executa
- Loads calculam endereço + verificam hazards de memória
- Stores aguardam: endereço calculado aqui, escrita adiada até commit

### 3. Write Result
- Resultado publicado no **CDB** (Common Data Bus) → lido por RS aguardando e pela entrada no ROB
- Entrada do ROB recebe o valor e marca **Ready = 1**
- RS liberada

### 4. Commit
- Instrução na **cabeça do ROB** com Ready = 1 → commit
- Resultado gravado no arquivo de registradores arquitetural
- ROB remove a entrada (head pointer avança)
- **Store**: escreve valor na memória apenas neste estágio
- **Branch errado**: flush de todo o ROB, restaura estado arquitetural, redireciona PC

## Commit em Ordem → Interrupções Precisas

Mesmo com execução fora de ordem, commit é estritamente em ordem de programa. Qualquer exceção ou interrupção é processada somente no momento do commit — estado arquitetural sempre reflete instruções completadas em ordem.

## Eliminação de Hazards WAR e WAW

Como em Tomasulo, renaming via ROB elimina:
- **WAR** (write after read): instrução posterior pode escrever no ROB sem afetar leitores anteriores
- **WAW** (write after write): cada instrução tem sua própria entrada no ROB; commit em ordem garante última versão

## Tomasulo com ROB vs. Tomasulo Original

| Aspecto | Tomasulo (sem ROB) | Tomasulo + ROB |
|---|---|---|
| Escrita em registrador | no Write Result | somente no Commit |
| Exceções precisas | não garantido | garantido |
| Especulação de controle | não | sim |
| Rollback | impossível | flush do ROB |

## Custos e Riscos

- tamanho do ROB limita janela de instruções em voo (típico: 128–512 entradas em designs modernos)
- energia desperdiçada em trabalho descartado por misspeculation
- **Spectre/Meltdown**: estado microarquitetural (cache) observável antes do commit → canal lateral

## Ver também
- [[Algoritmo de Tomasulo]] — mecanismo de base que ROB estende
- [[Previsão de Desvio]] — escolha do caminho especulativo
- [[Execução Fora de Ordem]] — pipeline out-of-order onde ROB opera
- [[Paralelismo em Nível de Instrução]] — ROB amplia janela de ILP explorável
- [[Escalonamento Dinâmico]] — scoreboard como alternativa sem renaming
- [[Múltipla Emissão e Superescalar]] — ROB essencial para superescalar especulativo
