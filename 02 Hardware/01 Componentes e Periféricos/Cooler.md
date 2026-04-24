---
title: Cooler
aliases:
  - Dissipador de Calor
  - Refrigeração de Processador
  - Water Cooler
  - Liquid Cooling
tags:
  - hardware
  - refrigeração
  - cooler
  - processador
date: 2026-04-12
source: "Clube do Hardware - Cap. 54"
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Hardware]]

# Cooler

Dispositivo para refrigeração de processadores. Remove calor via ar, líquido ou efeito elétrico (Peltier). Coolers de GPU seguem os mesmos princípios, mas geralmente direcionam ar quente para fora do gabinete.

## TDP

**Thermal Design Power** — quantidade máxima de calor que o cooler consegue dissipar (W).

> [!important] Regra
> TDP do cooler ≥ TDP do processador.

TDP do processador ≠ consumo real. É o pior caso possível. Consumo varia conforme carga.

## Mecanismo de Retenção

Específico por soquete — coolers **não são universais**. Verificar:
1. compatibilidade mecânica com o soquete
2. capacidade de dissipar o TDP do processador

Coolers premium têm retenção ajustável/substituível para múltiplos soquetes.

---

## Coolers a Ar

### Tipos

| Tipo     | Origem           | Desempenho | Ruído |
| -------- | ---------------- | ---------- | ----- |
| Stock / In-box | Acompanha CPU (box) | Baixo | Alto |
| Torre (avulso) | Comprado separado | Alto | Baixo/médio |

### Componentes

#### Base

Transfere calor do processador para o cooler.

| Atributo    | Impacto                                      |
| ----------- | -------------------------------------------- |
| Polimento   | Mais espelhada = mais área de contato = melhor transferência |
| Material    | Cobre > alumínio; cobre niquelado = visual cromado |

#### Composto Térmico (Pasta Térmica)

Preenche imperfeições microscópicas entre base e processador.

> [!warning] Atenção
> Pasta térmica não melhora o cooler — ela apenas viabiliza o contato. **Excesso piora** o desempenho. Diferença entre marcas é irrelevante para usuário comum.

Desempenho da transferência depende de: material da base, qualidade do polimento, pressão do cooler.

#### Heatpipes

Tubos de cobre que transferem calor da base ao dissipador por **mudança de estado** (troca de fase):

```
[LADO QUENTE]                        [LADO FRIO]
Base aquece líquido → vira vapor → sobe → resfria → vira líquido → desce
```

- Material: cobre (alaranjado) ou cobre niquelado (cromado)
- Configuração linear (base→dissipador) ou em U (centro na base, pontas no dissipador)
- Pode tocar diretamente o processador em alguns modelos

#### Câmara de Vapor

Heatpipe em formato de placa. Mais comum em coolers de GPU.

#### Dissipador de Calor

Material: alumínio, cobre ou cobre niquelado. Dezenas de aletas trocam calor com o ar.

- **Ativo**: tem ventoinha — padrão
- **Passivo**: sem ventoinha — para sistemas silenciosos

Ver [[Ventoinha]] para especificações técnicas da ventoinha.

#### Mecanismo de Retenção (backplate)

Placa instalada no verso da placa-mãe. Distribui pressão e evita empenamento.

---

## Coolers Termoelétricos (TEC / Peltier)

Usam **efeito Peltier**: placa semicondutora transfere calor eletricamente de um lado ao outro. Alguns coolers a ar topo de linha combinam esta tecnologia com heatpipes.

---

## Refrigeração Líquida

Substitui heatpipes por líquido circulante. Maior desempenho e menor ruído que coolers a ar com ventoinha.

> [!note] Nomenclatura
> "Water cooler" é tecnicamente incorreto — o líquido é água + glicol (propilenoglicol ou etilenoglicol, ~50/50), similar ao radiador automotivo. Termo correto: **liquid cooling**.

**Componentes principais:**
- **Bloco / Placa fria** — equivalente à base; líquido entra frio, sai quente
- **Bomba** — circula o líquido; base usa padrão de furação de ventoinha
- **Tanque de expansão / Reservatório** — evita pressão excessiva; frequentemente acoplado à bomba
- **Radiador / Trocador de calor** — resfria o líquido; usa uma ou mais ventoinhas

### Tamanhos de Radiador

| Ventoinhas | Tamanho 120 mm | Tamanho 140 mm |
| ---------- | -------------- | -------------- |
| 1          | 120 mm         | 140 mm         |
| 2          | 240 mm         | 280 mm         |
| 3          | 360 mm         | 420 mm         |

Radiadores double-fan (ventoinhas frente e verso) existem para 120 e 140 mm.

### Tipos de Sistema

| Tipo    | Complexidade | Custo | Flexibilidade                          |
| ------- | ------------ | ----- | -------------------------------------- |
| Avulso  | Alta         | Alto  | Máxima — expansível para GPU, chipset  |
| Externo | Média        | Alto  | Radiador/bomba/reservatório externos; requer gabinete com furos para mangueiras |
| Selado (AIO) | Baixa   | Médio | Bomba embutida no bloco; líquido pré-instalado; pasta térmica pré-aplicada |

> [!tip] AIO (All-In-One)
> Sistema selado é o mais popular. Instala como ventoinha — o radiador substitui a ventoinha traseira do gabinete. Indicado para quem quer desempenho líquido sem montar manualmente.

---

## Relações

| Sujeito              | Predicado       | Objeto                  |
| -------------------- | --------------- | ----------------------- |
| Cooler               | refrigera       | [[Processador]]         |
| TDP do cooler        | deve ser ≥      | TDP do processador      |
| Heatpipe             | usa             | mudança de estado       |
| Pasta térmica        | preenche        | imperfeições de contato |
| Refrigeração líquida | supera em ruído | cooler a ar             |
| AIO                  | é subtipo de    | refrigeração líquida    |
| Ventoinha            | compõe          | [[Ventoinha]]           |
| Radiador AIO         | instalado como  | [[Ventoinha]] no gabinete |
| Cooler               | instalado em    | [[Gabinete]]            |

---

## Ver também

- [[Ventoinha]] — especificações, rolamentos, conectores
- [[Gabinete]] — suporte a radiadores, posicionamento
- [[Processador]] — TDP, soquetes
