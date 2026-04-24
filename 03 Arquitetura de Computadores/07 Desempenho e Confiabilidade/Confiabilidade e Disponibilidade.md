---
title: Confiabilidade e Disponibilidade
aliases:
  - dependability
  - MTTF
  - MTTR
  - MTBF
  - availability
  - confiabilidade de sistema
  - AFR
tags:
  - computação/arquitetura
  - sistemas
date: 2026-04-22
source: "P&H Cap. 1 §1.7, Apêndice D §D.3–D.4"
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Confiabilidade e Disponibilidade

Propriedades quantificáveis de um sistema que descrevem sua capacidade de operar corretamente ao longo do tempo e de se recuperar após falhas.

## Relações (SPO)
- MTTF → mede → tempo médio até falha de um componente
- MTTR → mede → tempo médio de reparo após falha
- Disponibilidade → é → MTTF / (MTTF + MTTR)
- Fault → pode causar → Error → pode causar → Failure
- [[RAID]] → aumenta → confiabilidade do subsistema de armazenamento

## Hierarquia Fault → Error → Failure

| Nível | Definição | Exemplo |
|---|---|---|
| **Fault** (falha) | defeito físico ou lógico no componente | bit preso em 1 por radiação |
| **Error** (erro) | valor incorreto no estado do sistema | leitura retorna bit errado |
| **Failure** (falha observável) | comportamento incorreto visível ao serviço | cálculo retorna resultado errado |

Um fault não causa necessariamente failure — depende de ativação e propagação. Mecanismos de detecção e correção interrompem a cadeia.

## Métricas Principais

$$\text{MTBF} = \text{MTTF} + \text{MTTR}$$

$$\text{Disponibilidade} = \frac{\text{MTTF}}{\text{MTTF} + \text{MTTR}}$$

$$\text{AFR} = \frac{8760}{\text{MTTF (horas)}}$$

| Métrica | Significado |
|---|---|
| MTTF | Mean Time To Failure — tempo médio até a próxima falha |
| MTTR | Mean Time To Repair — tempo médio de recuperação |
| MTBF | Mean Time Between Failures = MTTF + MTTR |
| AFR | Annual Failure Rate — fração da população que falha por ano |
| Disponibilidade | fração do tempo em que o sistema opera corretamente |

> [!note] MTTF real vs. fabricante
> Fabricantes citam MTTF de 600.000–1.000.000 h para discos. Estudos em campo (Gray e van Ingen 2005) medem AFR de 3–7% para discos ATA → MTTF real de 125.000–300.000 h. Usar dados de campo em projetos de confiabilidade.

## Confiabilidade de Sistemas em Série

Componentes em série: qualquer falha derruba o sistema.

$$\frac{1}{\text{MTTF}_\text{sistema}} = \sum_{i} \frac{1}{\text{MTTF}_i}$$

Exemplo: 160 discos com MTTF = 125.000 h cada:

$$\text{MTTF}_\text{conjunto} = \frac{125.000}{160} \approx 781 \text{ h}$$

Resultado: conjunto de muitos discos sem redundância falha em semanas.

## Técnicas para Aumentar Dependabilidade

| Técnica | Mecanismo |
|---|---|
| Redundância temporal | re-executa operação suspeita |
| Redundância de informação | ECC, paridade, CRC |
| Redundância de componente | RAID, replicação, hot spare |
| Checkpointing | salva estado periodicamente para rollback |

## Nines de Disponibilidade

| Disponibilidade | Downtime/ano |
|---|---|
| 90% (1 nine) | 36,5 dias |
| 99% (2 nines) | 3,65 dias |
| 99,9% (3 nines) | 8,76 h |
| 99,99% (4 nines) | 52,6 min |
| 99,999% (5 nines) | 5,26 min |

## Ver também
- [[RAID]] — redundância em storage para aumentar MTTF do subsistema
- [[Hierarquia de Memória]] — ECC em DRAM como proteção de error
- [[WSC — Warehouse Scale Computer]] — confiabilidade como design constraint em escala
- [[Teoria de Filas para IO]] — modelagem de desempenho de I/O sob carga
