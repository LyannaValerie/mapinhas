---
title: Bateria
aliases:
  - Bateria Recarregável
  - Li-Ion
  - NiMH
  - Ni-Cd
  - Chumbo-Ácido
  - VRLA
tags:
  - hardware
  - alimentação
  - bateria
date: 2026-04-12
source: "Clube do Hardware - Cap. 58"
---

# Bateria

Dispositivo de armazenamento de energia. Usado em notebooks, nobreaks, automóveis, cadeiras de rodas elétricas, etc.

## Autonomia e Capacidade

$$Wh = \frac{mAh \cdot V}{1000}$$

**Taxa de descarga C:**
| Taxa | Tempo de descarga completa |
| ---- | -------------------------- |
| 0,5 C | 2 horas                   |
| 1 C   | 1 hora                    |
| 2 C   | 30 minutos                |
| 15 C  | 4 minutos (Ni-Cd)         |

---

## Comparativo Geral

| Tecnologia   | Ciclos | Autodescarga/mês | Efeito memória | Portáteis | Uso principal           |
| ------------ | ------ | ---------------- | -------------- | --------- | ----------------------- |
| Chumbo-ácido (VRLA) | 200–300 | 3,5% (25 °C) | Não | Não (pesada) | Nobreaks, automóveis |
| Ni-Cd        | 1.500  | ~10% (primeiras 24h) + 10%/mês | Sim (grave) | Não (obsoleto) | Ferramentas, histórico |
| NiMH         | 300    | 20% (24h) + 0,5–4%/dia | Sim (leve) | Não (obsoleto) | Pilhas recarregáveis |
| Li-Ion       | 1.000  | 1,5–2%/mês     | Não            | Sim       | Notebooks, smartphones  |

---

## Chumbo-Ácido

**Química:** cátodo = dióxido de chumbo; ânodo = chumbo; eletrólito = ácido sulfúrico.

Após descarga: ambas as placas viram sulfato de chumbo; eletrólito vira basicamente água.

**Problema tradicional (eletrólito líquido):**
- Só opera em pé
- Requer reposição de água destilada

**VRLA / SLA (selado/sem manutenção):**

| Subtipo | Eletrólito               |
| ------- | ------------------------ |
| AGM     | Absorvido em manta de fibra de vidro |
| Gel     | Sílica pirogênica → gelatinoso |

- Opera em qualquer posição
- Retém H₂ e O₂ internamente; válvula expele se pressão exceder limite
- Sem reposição de água

> [!warning] Sulfatação
> Descarregada completamente → cristalização do sulfato de chumbo → difícil ou impossível recarregar. Guardar sempre carregada.

**Temperatura e vida útil** (exemplo: 10 anos a 25 °C):
- 33 °C → ~5 anos
- 42 °C → ~1 ano

---

## Níquel-Cádmio (Ni-Cd)

**Química:** cátodo = hidróxido de níquel; ânodo = cádmio; eletrólito = hidróxido de potássio.

**Vantagens:**
- 1.500 ciclos
- Opera a baixas temperaturas
- Taxa de descarga até 15 C
- Pode ser armazenada descarregada

**Desvantagens:**
- **Efeito memória grave** — bateria "memoriza" ponto de recarga; exige descarga total antes de recarregar
- Autodescarga: 10% nas primeiras 24h + 10%/mês (20 °C)
- Cádmio: altamente tóxico → descarte complexo
- Em desuso em portáteis

---

## Hidreto Metálico de Níquel (NiMH)

**Química:** cátodo = hidróxido de níquel; ânodo = liga absorvedora de hidrogênio (sem cádmio).

**Vantagens sobre Ni-Cd:** maior densidade energética; menos tóxica.

**Desvantagens:**
- Autodescarga: 20% em 24h + 0,5–4%/dia (20 °C) — muito alta
- Esquenta bastante durante recarga
- Recarga mais lenta que Ni-Cd
- Efeito memória brando
- Apenas 300 ciclos
- Descontinuada em portáteis

---

## Lítio-Íon (Li-Ion)

**Química:** cátodo = composto de lítio (varia por fabricante); ânodo = grafite; eletrólito = sal de lítio.

**Vantagens:**
- Maior densidade energética — mais leve
- 1.000 ciclos
- Sem efeito memória
- Autodescarga mínima: 1,5–2%/mês

**Desvantagens:**
- Autonomia degrada com o tempo (irreversível)
- **Danificada permanentemente se descarregada abaixo de tensão mínima** — não guardar descarregada
- Inflamável — risco de incêndio

---

## Relações

| Sujeito       | Predicado         | Objeto                        |
| ------------- | ----------------- | ----------------------------- |
| Li-Ion        | usada em          | Notebooks, smartphones        |
| VRLA/SLA      | usada em          | [[Equipamentos de Proteção#Nobreaks|Nobreaks]], automóveis |
| Ni-Cd         | apresenta         | efeito memória grave          |
| Li-Ion        | não tolera        | descarga abaixo de tensão mínima |
| Taxa C        | mede              | velocidade de descarga        |
| Wh            | converte de       | mAh · V / 1000                |

---

## Ver também

- [[Equipamentos de Proteção]] — nobreaks usam baterias VRLA; autonomia em VA/W
- [[Fonte de Alimentação]] — +5VSB alimenta stand-by; PSU full range elimina necessidade de estabilizador
