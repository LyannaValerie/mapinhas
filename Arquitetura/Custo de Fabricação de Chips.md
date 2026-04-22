---
tags:
  - arquitetura/fabricação
  - hardware/chip
aliases:
  - Die Yield
  - Wafer Yield
  - custo de chip
---

# Custo de Fabricação de Chips

Modelo quantitativo para estimar fração de dies funcionais em um wafer e custo por die.

## Fórmula de Rendimento (Die Yield)

$$\text{Die yield} = \text{Wafer yield} \times \frac{1}{\left(1 + \text{Defects per unit area} \times \text{Die area}\right)^N}$$

Onde:
- **Wafer yield**: fração de wafers que passam no teste inicial (valores ruins descartados antes)
- **Defects per unit area**: defeitos aleatórios de fabricação por cm²
- **Die area**: área do die em cm²
- **N**: fator de complexidade do processo (dificuldade de fabricação)

> [!note] Fórmula de Bose-Einstein
> Modelo empírico derivado de observação de linhas de fabricação reais. Ainda válido em processos modernos.

## Parâmetros Típicos (2017)

| Processo | Defeitos/cm² | N |
|---|---|---|
| 28 nm | 0,012–0,016 | 7,5–9,5 |
| 16 nm | 0,016–0,047 | 10–14 |

## Custo por Die

$$\text{Cost per die} = \frac{\text{Cost per wafer}}{\text{Dies per wafer} \times \text{Die yield}}$$

$$\text{Dies per wafer} \approx \frac{\pi \times (\text{Wafer radius})^2}{\text{Die area}} - \frac{\pi \times \text{Wafer diameter}}{\sqrt{2 \times \text{Die area}}}$$

> [!example] Exemplo comparativo
> Wafer de 16 nm = ~$7.000.
> - Die 1,00 cm² → custo ~$16
> - Die 2,25 cm² → custo ~$58 (~4× mais caro para ~2× mais área)
>
> O custo por die cresce **aproximadamente com o quadrado da área**.

## Implicação para o Designer

- Fabricante controla: custo do wafer, wafer yield, defeitos por área
- Designer controla: **área do die**
- Funções incluídas/excluídas no die e número de pinos de I/O determinam a área
- Menos área → mais dies por wafer → menor custo unitário → maior margem

## Redundância para Aumentar Yield

DRAMs incluem células redundantes: certo número de linhas/colunas defeituosas pode ser substituído por cópias reserva. Estratégia padrão em memórias para elevar o yield.

## Gamas de Die Area em Uso

| Tipo | Área típica |
|---|---|
| IoT/controle embarcado | < 0,01 cm² |
| Embarcado 32-bit low-end | ~0,05 cm² |
| Processador convencional | 1,00–2,25 cm² |
| Servidor / GPU high-end | até ~8 cm² |

## Custo de Fabricação vs. Custo de Operação

- **CAPEX**: custo de compra do hardware (amortizado em 3–4 anos em WSC)
- **OPEX**: custo operacional (energia, resfriamento, infraestrutura)
- Em WSC, CAPEX ≈ 50% do custo mensal; energia ≈ 40%

## Relações

- [[WSC — Warehouse Scale Computer]] — CAPEX/OPEX em escala
- [[Lei de Moore]] — escalonamento de transistores afeta área e defeitos
- [[Dependabilidade]] — defeitos de fabricação → falhas transientes e permanentes
