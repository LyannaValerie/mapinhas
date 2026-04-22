---
tags:
  - arquitetura/confiabilidade
  - arquitetura/desempenho
aliases:
  - Dependability
  - MTTF
  - confiabilidade
  - disponibilidade
---

# Dependabilidade

Capacidade de um sistema de entregar serviço correto continuamente. Formalizações de "confiabilidade" como métricas quantificáveis.

## Estados do Sistema

Sistema opera em dois estados:
- **Serviço correto** (accomplishment)
- **Interrupção** (interruption)

Transições:
- **Falha** (failure): correto → interrupção
- **Restauração** (restoration): interrupção → correto

## Métricas de Confiabilidade

### MTTF — Mean Time To Failure

Tempo médio até a primeira falha (módulo sem reparo).

### MTTR — Mean Time To Repair

Tempo médio para restaurar o serviço após falha.

### MTBF — Mean Time Between Failures

$$\text{MTBF} = \text{MTTF} + \text{MTTR}$$

> [!note]
> MTTF é mais preciso que MTBF quando o componente pode ser reparado. MTBF implica que o tempo de reparo conta como "entre falhas".

### FIT — Failures In Time

Taxa de falha normalizada por $10^9$ horas de operação.

$$\text{FIT} = \frac{10^9}{\text{MTTF (em horas)}}$$

Exemplo: MTTF = 1.000.000 h → FIT = 1000.

### Disponibilidade do Módulo

$$\text{Disponibilidade} = \frac{\text{MTTF}}{\text{MTTF} + \text{MTTR}}$$

> [!example] Exemplo: subsistema de discos
> - 10 discos, MTTF = 1.000.000 h cada
> - 1 controlador ATA, MTTF = 500.000 h
> - 1 fonte de alimentação, MTTF = 200.000 h
> - 1 ventilador, MTTF = 200.000 h
> - 1 cabo, MTTF = 1.000.000 h
>
> MTTF do sistema = recíproco da soma das taxas de falha:
> $$\frac{1}{\text{MTTF}_{\text{sys}}} = \frac{10}{10^6} + \frac{1}{5\times10^5} + \frac{1}{2\times10^5} + \frac{1}{2\times10^5} + \frac{1}{10^6}$$

## Par Redundante de Fontes

$$\text{MTTF}_{\text{par}} = \frac{\text{MTTF}^2}{2 \times \text{MTTR}}$$

Duas fontes com MTTF = 200.000 h e MTTR = 24 h:

$$\text{MTTF}_{\text{par}} = \frac{200.000^2}{2 \times 24} \approx 833.000.000\text{ h}$$

~4.150× mais confiável que uma fonte simples.

## SLA — Service Level Agreement

Acordo formal que define o nível mínimo de serviço. Violação = **service interruption**. Relevante para provedores de infraestrutura.

## Regra de Ponto Único de Falha

> [!warning] Pitfall clássico
> Dependabilidade é tão forte quanto o elo mais fraco. Redundar fontes não adianta se o único ventilador falhar.
> **Regra:** todo componente crítico deve ser redundante (sem single point of failure).

## Componentes de Hardware e Taxas Reais

- Fabricantes declaravam MTTF de 1.000.000–1.500.000 h para discos
- Estudos de campo (Pinheiro et al., 2007): taxa anual de falha real = 2%–10%
- MTTF real = 2–10× pior que o declarado pelo fabricante

## Relações

- [[ECC de Memória]] — mecanismo de correção de erro em DRAM
- [[RAID]] — redundância em disco para disponibilidade
- [[WSC — Warehouse Scale Computer]] — dependabilidade em escala
- [[Regras Práticas de Arquitetura]] — "Design with no single point of failure"
