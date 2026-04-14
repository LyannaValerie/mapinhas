---
title: Processadores Intel Híbridos (P+E)
tags:
  - hardware
  - arquitetura-x86
  - história
  - processadores
  - intel
aliases:
  - processadores híbridos Intel
  - processadores heterogêneos Intel
  - núcleos P
  - núcleos E
  - Lakefield
  - Alder Lake
  - Raptor Lake
  - big.LITTLE x86
---

# Processadores Intel Híbridos (P+E)

Período: **2020–presente**. Processadores com núcleos de duas microarquiteturas distintas no mesmo die.

---

## Conceito

| Núcleo | Nome | Microarquitetura | Foco |
|--------|------|-----------------|------|
| **P** (Performance) | "grande" | Família 6 — Golden Cove, Raptor Cove… | Desempenho máximo |
| **E** (Eficiente) | "pequeno" | Família Atom — Tremont, Gracemont | Baixo consumo |

Ideia já usada em smartphones (ARM big.LITTLE) — primeira aplicação em x86.

Objetivo: comportar-se como família Atom (< 10 W) quando carga for leve, e como família 6 quando necessário.

---

## Modelos Lançados

| Ano | Produto | Núcleos P | Núcleos E | Core i |
|-----|---------|-----------|-----------|--------|
| 2020 | **Lakefield** | Sunny Cove | Tremont | — (fora da sequência) |
| 2021 | **Alder Lake** | Golden Cove | Gracemont | 12ª geração |
| 2022 | **Raptor Lake** | Raptor Cove | Gracemont | 13ª geração |

---

## Restrições de Instruções

Processador híbrido suporta apenas instruções suportadas por **ambas** as microarquiteturas.

| Instrução | Núcleo P | Núcleo E | Híbrido suporta? |
|-----------|---------|---------|----------------|
| AVX | Sunny Cove ✓ | Tremont ✗ | **Lakefield: NÃO** |
| AVX / AVX2 | Golden Cove ✓ | Gracemont ✓ | **Alder/Raptor Lake: SIM** |
| AVX-512 | Golden Cove ✓ | Gracemont ✗ | **Alder/Raptor Lake: NÃO** |

> [!important] Razão para ausência de AVX-512 nos Core i de 12ª/13ª geração
> Golden Cove suporta AVX-512, mas Gracemont não. Como L3 e instruções são compartilhados entre todos os núcleos, AVX-512 foi desabilitado nos modelos híbridos cliente.

---

## Características do Design Híbrido

- Núcleos E **não** suportam Hyper-Threading
- **Cache L3 compartilhado** entre todos os núcleos P e E
- Escalonador do SO precisa distinguir núcleos P dos E para atribuir tarefas corretamente

---

## Processadores

### Lakefield (2020)

- Apenas **2 modelos**: Core i3 e Core i5 (rotulados "Core com tecnologia híbrida" — fora da sequência de gerações Core i)
- Curiosidade: traziam **8 GiB de LPDDR4X integrada** diretamente no processador
- Caráter experimental — Intel testando receptividade do mercado antes de adotar na linha principal

### Alder Lake (2021 — Core i 12ª geração)

- Adoção na **linha principal**: Core i3/i5/i7/i9
- Também lançados: **Celeron** e **Pentium Gold** híbridos
- Suporte a AVX2 (Gracemont viabilizou)

### Raptor Lake (2022 — Core i 13ª geração)

- Refinamento do Alder Lake (núcleos P: Raptor Cove; núcleos E: Gracemont — mesmo da geração anterior)
- Core i3/i5/i7/i9 + Celeron e Pentium Gold

---

## Ver também

- [[Microarquitetura Intel Família 6 Parte 3]] — núcleos P: Golden Cove (Alder), Raptor Cove (Raptor), Sunny Cove (Lakefield)
- [[Microarquitetura Intel Família Atom]] — núcleos E: Tremont (Lakefield), Gracemont (Alder/Raptor)
- [[Desempenho do Processador]] — multi-core, escalonamento de threads, desempenho/watt
- [[Instruções SIMD]] — AVX2 habilitado pelo Gracemont; AVX-512 bloqueado pela incompatibilidade
- [[Cache]] — L3 LLC compartilhado entre núcleos P e E
- [[Gerenciamento de Consumo Elétrico]] — núcleos E para cargas leves = economia energia
