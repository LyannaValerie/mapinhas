---
title: Microarquitetura Netburst — Intel Família F
tags:
  - hardware
  - arquitetura-x86
  - história
  - processadores
  - intel
aliases:
  - Netburst
  - família F
  - família 7 Intel
  - Pentium 4
  - Pentium D
  - Pentium 4 HT
  - Xeon Netburst
---

# Microarquitetura Netburst — Intel Família F

Período: **2000–2005** (codinomes até 2006). CPUID família = F, família estendida = 0.

> [!warning] Microarquitetura fracassada
> Netburst priorizou clocks altos via pipeline longo. Resultado: alto consumo, geração excessiva de calor e desempenho por clock inferior ao P6. Intel voltou à linhagem P6 após o Netburst. Pentium III de mesmo clock era **mais rápido** que o Pentium 4 original.

> [!note] Por que família F e não 7?
> Intel reservou o número 7 para processadores Itanium (IA-64, incompatível com x86). Quando lançou o Pentium 4, usou família F (15 decimal) + campo "família estendida" = 0.

---

## Diferenças em relação à P6

| Atributo | P6 | Netburst |
|----------|-----|---------|
| Pipeline | 12 estágios | **20 estágios** (Willamette/Northwood) ou **31** (Prescott) |
| Cache L1 instruções | Cache normal | **Cache de traço** (µops já decodificadas) |
| Caminho L1D ↔ L2 | 64 bits | **256 bits** (4× mais rápido) |
| Registradores físicos | 40 (RRF) | **128 (PRF)** |
| FSB | 1 dado/ciclo | **4 dados/ciclo (QDR)** |
| Decodificadores | 3 (2 simples + 1 complexo) | **1** |
| Agendadores | 1 estação de reserva | **4 agendadores** por tipo de instrução |
| Buffers de reordenamento | 1 (40 µops) | **2** (inteiros + FP), **128 µops cada** |
| HT | Não (até Pentium III) | **Sim** (Pentium 4 HT, 2002) |
| Novas instruções | SSE | **SSE2, SSE3** |
| 64 bits | Não | Alguns modelos |
| Virtualização | Não | Alguns modelos |

---

## Pipeline Netburst (20 estágios — Willamette/Northwood)

```
PONTEIRO PRÓX. µOP (×2)
→ BUSCA PRÓX. µOP (×2)
→ DRIVE
→ ALOCAÇÃO DE RECURSOS
→ RENOMEAMENTO DE REGISTRADORES (×2)
→ FILA
→ AGENDAMENTO (×3)
→ ENVIO (×2)
→ REGISTRADORES FÍSICOS / PRF (×2)
→ EXECUÇÃO
→ FLAGS
→ VERIFICAÇÃO DE DESVIOS
→ DRIVE
```

**Hiperpipeline** = pipeline com muitos estágios. Cada estágio tem menos portas lógicas → permite clock interno maior. Tradeoff: instrução demora mais ciclos para completar.

> [!tip] Netburst leu a física corretamente — mas até certo ponto
> Mais estágios = clock maior. Funcionou até ~3 GHz. A partir daí o calor impediu aumentar o clock, e o alto CPI (ciclos por instrução) tornou o processador ineficiente.

Prescott (31 estágios): Intel não publicou detalhes dos estágios adicionais.

---

## Cache de Traço

Substitui o cache L1 de instruções convencional.

| | Cache L1 convencional | Cache de traço (Netburst) |
|--|----------------------|--------------------------|
| Posição no pipeline | Antes do decodificador | **Depois** do decodificador |
| O que armazena | Instruções x86 | **µops já decodificadas** |
| Capacidade | KiB de bytes | 12.288 µops (12 kµops) = ~150 KiB (µops de 100 bits) |
| BTB dedicado | Não | Sim (512 entradas) |

Vantagem: loops e desvios previstos = µops já prontas, sem re-decodificar.  
Funcionalidade similar ao **DSB** (Decoded Stream Buffer) do Sandy Bridge (cap. 25).

---

## Decodificador de Instruções

**Único decodificador** (vs. 3 na P6). Mesmo princípio: simples→1 µop, complexas→1–4, raras→MSROM.  
Menos usado na teoria (cache de traço absorve iterações) — na prática, desvios errados forçam re-decodificação.

---

## Execução das Microinstruções

### Agendadores (4 tipos)

| Agendador | Seleciona | Envia para |
|-----------|----------|-----------|
| **Rápido** | Instruções de 1 ciclo (soma/subtração inteiros, lógicas, desvios) | Portas 0 e 1 (**2 µops/ciclo**) |
| **Lento / FP geral** | FP e SIMD | Porta 1 |
| **FP simples** | FP simples | Porta 0 |
| **Memória** | Load/store | Porta 0 (dado), porta carga ou porta armazenamento |

### Unidades de Execução (4 portas)

| Unidade | Pipeline? | Função | Velocidade |
|---------|-----------|--------|-----------|
| **ALU SIMPLES 0** | — | Soma/sub inteiros, lógica, desvios; dado inteiro p/ armazenamento | **2 µops/ciclo** (2× clock) |
| **ALU SIMPLES 1** | — | Soma/sub inteiros | **2 µops/ciclo** (2× clock) |
| **ALU COMPLEXA** | Não | Shift/rotação, multiplicação/divisão inteira | Vários ciclos |
| **FP MOV** | — | FP/SIMD movimentação; dado FP p/ armazenamento | — |
| **FPU** | — | Soma, sub, mul, div FP completo | — |
| **AGU CARGA** | — | Endereço físico para leitura | — |
| **AGU ARMAZENAMENTO** | — | Endereço físico para escrita | — |

> [!note] ALU SIMPLES dupla velocidade
> ALU SIMPLES 0 e 1 operam a **2× o clock interno** do processador — "doubled-pumped ALU". Truque para aumentar throughput de inteiros sem aumentar portas.

---

## Barramento Externo (QDR)

Netburst transfere **4 dados por pulso de clock** (QDR — Quad Data Rate).

| Processador | Clock externo real | Notação Intel | Largura de banda (64b) |
|-------------|-------------------|--------------|----------------------|
| Pentium III | 100 MHz | 100 MHz | 800 MB/s |
| Pentium 4 | 100 MHz | **400 MT/s** | **3,2 GB/s** |
| Pentium 4 HT | 133 MHz | **533 MT/s** | 4,3 GB/s |
| Pentium 4 HT | 200 MHz | **800 MT/s** | 6,4 GB/s |

> [!warning] Clock externo inflado
> "400 MHz" do Pentium 4 = 100 MHz real × 4. Notação correta: **MT/s** (megatransferências/segundo) ou "400 QDR".

---

## Processadores

### Pentium 4 — 2000 (1 núcleo)

| Codinome | Ano | Processo | L2 | FSB | Soquete |
|---------|-----|----------|-----|-----|--------|
| Willamette | 2000 | 180 nm | 256 KiB | 400 MT/s | 423 / 478 |
| Northwood | 2002 | 130 nm | 512 KiB | 400 / 533 MT/s | 478 |
| Prescott | 2004 | 90 nm | 1 MiB | 533 MT/s | 478 / LGA775 |

### Pentium 4 HT — 2002–2006 (1 núcleo + HT)

Primeiro: Northwood 3,06 GHz (2002) — primeiro processador Intel com Hyper-Threading.

| Codinome | Ano | Processo | L2 | FSB | Soquete |
|---------|-----|----------|-----|-----|--------|
| Northwood | 2003 | 130 nm | 512 KiB | 800 MT/s | 478 |
| Prescott | 2004 | 90 nm | 1 MiB | 533 / 800 MT/s | 478 / LGA775 |
| Prescott-2M | 2005 | 90 nm | 2 MiB | 800 MT/s | LGA775 |
| Cedar Mill | 2006 | 65 nm | 2 MiB | 800 MT/s | LGA775 |

### Pentium 4 Extreme Edition — 2003 (1 núcleo + HT, entusiasta)

Multiplicador destravado (overclock). 

| Codinome | L2 | L3 | FSB |
|---------|-----|-----|-----|
| Gallatin | 512 KiB | 2 MiB | 800 / 1066 MT/s |
| Prescott-2M | 2 MiB | — | 1066 MT/s |

### Pentium D — 2005 (2 núcleos, **sem** HT)

Primeiro dual-core Intel. Cache L2 **não compartilhado** entre núcleos.

| Codinome | Ano | Processo | L2/núcleo | FSB |
|---------|-----|----------|----------|-----|
| Smithfield | 2005 | 90 nm | 1 MiB | 533 / 800 MT/s |
| Presler | 2006 | 65 nm | 2 MiB | 800 MT/s |

### Pentium Extreme Edition — 2005 (2 núcleos + HT = 4 threads)

Pentium D com HT + multiplicador destravado. SO vê 4 processadores.

### Pentium 4-M / Mobile Pentium 4 / Mobile Pentium 4 HT

| Modelo | Diferencial |
|--------|------------|
| Pentium 4-M (2002) | Northwood, tensão reduzida, SpeedStep, 1 núcleo |
| Mobile Pentium 4 (2003) | Pentium 4-M com FSB 533 MT/s |
| Mobile Pentium 4 HT | Mobile Pentium 4 + HT |

> [!warning] Pentium 4-M ≠ Pentium M
> Pentium M é família 6 (cap. 24). Pentium 4-M é Netburst para portáteis.

### Celeron / Celeron D / Mobile Celeron (Netburst)

Versões de baixo custo do Pentium 4 com cache L2 reduzido.

> [!warning] Celeron D ≠ dual-core
> "D" = **Desktop**, não Dual-core. Mesmo nome, significados diferentes que no Pentium D.

| Modelo | Codinome | L2 | FSB |
|--------|---------|-----|-----|
| Celeron | Willamette-128 | 128 KiB | 400 MT/s |
| Celeron | Northwood-128 | 128 KiB | 400 MT/s |
| Celeron D | Prescott-256 | 256 KiB | 533 MT/s |
| Celeron D | Cedar Mill-512 | 512 KiB | 533 MT/s |
| Mobile Celeron | Northwood-256 | 256 KiB | 400 MT/s |

### Xeon (Netburst) — SMP até 2 processadores

| Codinome | Processo | L2 | L3 | FSB | Soquete |
|---------|---------|-----|-----|-----|--------|
| Foster | 180 nm | 256 KiB | — | 400 MT/s | 603 |
| Prestonia | 130 nm | 512 KiB | — | 400 / 533 MT/s | 603/604 |
| Gallatin | 130 nm | 512 KiB | 1/2 MiB | 533 MT/s | 604 |
| Nocona | 90 nm | 1 MiB | — | 800 MT/s | 604 |
| Irwindale | 90 nm | 2 MiB | — | 800 MT/s | 604 |
| Paxville-DP | 90 nm | 2 MiB/núcleo | — | 800 MT/s | 604 |
| Dempsey | 65 nm | 2 MiB/núcleo | — | 667 / 1066 MT/s | LGA771 |

### Xeon MP (Netburst) — SMP até 4 ou 8 processadores

| Codinome | Núcleos | Vias | L3 | FSB |
|---------|---------|------|-----|-----|
| Foster MP | 1 | 4 | 512 KiB / 1 MiB | 400 MT/s |
| Gallatin | 1 | 4 | 1/2/4 MiB | 400 MT/s |
| Cranford | 1 | 4 | — | 667 MT/s |
| Potomac | 1 | 4 | 4/8 MiB | 667 MT/s |
| Paxville MP | 2 | 4 | — | 667 / 800 MT/s |
| Tulsa | 2 | 8 | 4/8/16 MiB | 667 / 800 MT/s |

---

## Ver também

- [[Microarquitetura P6 e Processadores Intel Família 6 Parte 1]] — predecessor; linhagem que substituiu o Netburst
- [[Desempenho do Processador]] — HT (primeiro processador Intel: Pentium 4 HT 2002); clock dinâmico
- [[Gerenciamento de Consumo Elétrico]] — SpeedStep (Pentium 4-M); P=CV²f explica o fracasso térmico do Netburst
- [[Instruções SIMD]] — SSE2 (Pentium 4), SSE3 (Prescott)
- [[Máquina Virtual]] — VT-x introduzido em alguns modelos Netburst
- [[Fabricação de Chips]] — evolução 180nm→130nm→90nm→65nm nos codinomes Netburst
