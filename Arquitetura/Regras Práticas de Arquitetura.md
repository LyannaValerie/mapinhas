---
tags:
  - arquitetura/principios
  - arquitetura/desempenho
aliases:
  - Rules of Thumb
  - regras de ouro
  - heurísticas de arquitetura
---

# Regras Práticas de Arquitetura

Heurísticas empíricas de Patterson & Hennessy para design e análise quantitativa de sistemas.

## As Seis Regras

### 1. Regra de Amdahl/Case
> Um sistema computacional balanceado precisa de aproximadamente **1 MB de memória principal** e **1 megabit por segundo de largura de banda de I/O** por **MIPS de desempenho de CPU**.

Balancear compute, memória e I/O proporcionalmente.

### 2. Regra 90/10 de Localidade
> Um programa executa aproximadamente **90% de suas instruções em 10% de seu código**.

Base para eficácia de [[Cache]] e previsão de desvios. Otimizar o hot path vale mais.

### 3. Regra de Largura de Banda
> Largura de banda cresce pelo menos com o **quadrado da melhoria na latência**.

Se latência melhora 10×, largura de banda deve crescer ≥100× para não se tornar gargalo.

### 4. Regra 2:1 de Cache
> A taxa de misses de um cache de mapeamento direto de tamanho $N$ é aproximadamente igual à de um cache set-associativo de 2 vias de tamanho $N/2$.

Dobrar a associatividade equivale a dobrar o tamanho para efeitos de miss rate. Útil para comparar alternativas de projeto de [[Cache]].

### 5. Regra de Dependabilidade
> **Projete sem ponto único de falha.**

Todo componente crítico deve ser redundante. Derivado diretamente da [[Lei de Amdahl]] aplicada a [[Dependabilidade]]: o elo mais fraco limita a confiabilidade do sistema.

### 6. Regra Watt-Ano (WSC)
> O custo totalmente carregado de **1 Watt por ano** em um Warehouse Scale Computer na América do Norte (2011), incluindo amortização de infraestrutura de energia e resfriamento, é aproximadamente **$2**.

Referência histórica para WSC. Quantifica o valor de eficiência energética em datacenters.

## Uso

- Estimativas rápidas de design
- Identificar gargalos prováveis antes de simulação detalhada
- Guiar alocação proporcional de recursos

> [!note] Complemento à Lei de Amdahl
> Regra 90/10 diz onde o tempo é gasto. [[Lei de Amdahl]] diz quanto vale otimizar essa fração. Juntas, orientam priorização de esforço de design.

## Relações

- [[Lei de Amdahl]] — base teórica das regras 2, 3, 5
- [[Cache]] — regras 2 e 4 diretamente aplicáveis
- [[Dependabilidade]] — regra 5
- [[Classes de Computadores]] — regra 6 específica para WSC
- [[Hierarquia de Memória]] — regra 1 (balanço memória/compute)
