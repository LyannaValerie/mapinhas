---
tags:
  - arquitetura/sistemas
  - arquitetura/história
aliases:
  - IoT
  - PMD
  - Personal Mobile Device
  - WSC
  - Warehouse Scale Computer
  - classes de computação
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Classes de Computadores

Taxonomia dos cinco mercados principais de computação (Patterson & Hennessy, 2017), cada um com requisitos e trade-offs distintos.

## 1. IoT / Embedded (Embarcado)

**Exemplos**: micro-ondas, máquinas de lavar, impressoras, switches de rede, automóveis.

| Característica | Valor |
|---|---|
| Área do chip | < 0,01 cm² (controle IoT), ~0,05 cm² (embarcado 32-bit) |
| Prioridade | Custo, consumo, tamanho |
| SO | Ausente ou RTOS mínimo |
| Memória | Restrita — code size é crítico |
| Desempenho | Secundário |

## 2. PMD — Personal Mobile Device

**Exemplos**: smartphones, tablets.

| Característica | Valor |
|---|---|
| Custo total | Centenas de dólares |
| Prioridade | Energia (bateria) > desempenho |
| Interface | Multimídia, touchscreen |
| SO | Android, iOS |
| Memória | Limitada, sem disco giratório |

Energia é a restrição dominante. Desempenho interessa dentro do envelope de potência disponível.

## 3. Desktop

**Exemplos**: notebooks, workstations, PCs.

| Característica | Valor |
|---|---|
| Faixa de preço | $300 (netbook) – $2.500 (workstation) |
| Prioridade | **Preço-desempenho** |
| Métricas | Compute performance + graphics performance |
| Tendência | Vendas em declínio desde ~2008 |

Primeiro mercado a receber microprocessadores de última geração. Referência histórica de benchmark por compatibilidade com SPEC CPU.

> [!note]
> Desde 2008, maioria dos desktops produzidos são laptops — operados a bateria.

## 4. Servidores

**Exemplos**: file servers, web servers, servidores de banco de dados.

| Característica | Valor |
|---|---|
| Prioridade | Disponibilidade, escalabilidade, throughput |
| Disponibilidade | Falha = perda de receita direta (ver tabela abaixo) |
| Escalonamento | Compute, memória, storage, I/O bandwidth |
| Benchmark | TPC-C/E (OLTP), SPECrate |

**Custo de indisponibilidade (por hora, valores aproximados):**

| Setor | Custo/hora |
|---|---|
| Finanças/corretagem | $6.450.000 |
| Energia | $2.817.000 |
| Telecom | $2.066.000 |
| Manufatura | $1.610.000 |
| Varejo | $650.000 |

*Fonte: Landstrom (2014) / Contingency Planning Research*

## 5. WSC — Warehouse Scale Computer

**Exemplos**: datacenters Google, Amazon AWS, Microsoft Azure.

| Característica | Valor |
|---|---|
| Escala | Dezenas de milhares de servidores |
| Prioridade | Custo/desempenho em escala, eficiência energética |
| Benchmark | SPECpower (ssj_ops/watt) |
| Métrica de energia | PUE (Power Utilization Effectiveness) |
| CAPEX | ~50% do custo mensal |
| Energia + infraestrutura | ~40% do custo mensal |

$$\text{PUE} = \frac{\text{Total Facility Power}}{\text{IT Equipment Power}}$$

PUE ideal = 1,0 (toda energia vai para TI). PUE real: 1,2–2,0.

> [!tip]
> WSC torna custo de operação (OPEX) tão relevante quanto custo de compra (CAPEX). Eficiência energética vira preocupação primária do arquiteto.

## Comparação Resumida

| Classe | Prioridade 1 | Prioridade 2 | Prioridade 3 |
|---|---|---|---|
| IoT/Embedded | Custo | Consumo | Tamanho |
| PMD | Energia | Custo | Desempenho |
| Desktop | Preço-desempenho | Graphics | — |
| Servidor | Disponibilidade | Throughput | Escalabilidade |
| WSC | Eficiência energética | Custo/perf | Disponibilidade |

## Relações

- [[Dependabilidade]] — disponibilidade de servidores e WSC
- [[Benchmarks de Desempenho]] — SPEC CPU (desktop), TPC (servidor), SPECpower (WSC)
- [[Gerenciamento de Consumo Elétrico]] — ACPI/DVFS aplicado a PMD e servidores
- [[Custo de Fabricação de Chips]] — custo unitário impacta PMD e IoT
- [[Concorrência e Paralelismo]] — servidores e WSC exploram RLP e TLP massivamente
