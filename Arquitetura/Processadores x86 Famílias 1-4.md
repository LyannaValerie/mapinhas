---
title: Processadores x86 — Famílias 1 a 4
tags:
  - hardware
  - arquitetura-x86
  - história
  - processadores
aliases:
  - família 1
  - família 2
  - família 3
  - família 4
  - 8086
  - 8088
  - V20
  - 286
  - 386
  - 486
  - 80286
  - 80386
  - 80486
---

# Processadores x86 — Famílias 1 a 4

Período: **1978–1995**. "Família" = valor retornado pela instrução `CPUID` (disponível apenas a partir de alguns modelos do 486).

> [!note] Família ≠ Geração Intel Core
> A partir da família 5, Intel passou a nomear por geração de Core i. Para evitar confusão, o livro usa "família" para os processadores históricos.

---

## Contexto histórico

- 8086/8088/286: Intel licenciou fabricação para AMD e outros (exigência da IBM para o IBM PC)
- A partir do 386: AMD, Cyrix e outros usaram **engenharia reversa** para criar chips compatíveis com o mesmo nome
- Intel processou os concorrentes por patente → a partir da família 5 cada fabricante seguiu caminho próprio

---

## Linha do Tempo

```
1978  8086, 8088
1982  286
1985  386DX
1987  386SX
1989  486DX
1991  486SX, 486DX2
1992  486SLC (Cyrix/TI)
1993  486DX4, 5x86 (AMD/Cyrix)
1994  486DLC (Cyrix/TI)
1995  → Família 5
```

---

## Família 1 — 8086, 8088, V20

**Processadores de 16 bits.** Todos usam a mesma arquitetura básica — processadores de qualquer fabricante eram internamente idênticos (exceto o V20).

| Processador | Fabricante | Barramento dados | Barramento endereços | Destaque |
|------------|-----------|-----------------|---------------------|---------|
| **8086** | Intel/AMD/outros | 16 bits | 20 bits | Primeiro x86 |
| **8088** | Intel/AMD/outros | **8 bits** | 20 bits | Usado no IBM PC original (circuitos de apoio 8 bits = mais baratos) |
| **V20** | NEC | 8 bits | 20 bits | Compatível pino a pino com 8088; mais rápido (engenharia reversa) |

Características comuns:
- Modo real apenas
- Registradores de 16 bits
- Acesso a até **1 MiB** de memória
- Suporte ao coprocessador matemático **8087**

---

## Família 2 — 286

**Único processador da família 2.** Introduziu o modo protegido de 16 bits — raramente usado na prática.

| Atributo | Valor |
|----------|-------|
| Barramento dados | 16 bits |
| Barramento endereços | 24 bits |
| Memória endereçável | até **16 MiB** |
| Coprocessador | **287** |

Novidades do modo protegido de 16 bits:
- Proteção de memória
- Multitarefa por hardware
- Memória virtual (até 1 GiB virtual + RAM)
- Acesso >1 MiB via **EMS** (Expanded Memory System) no modo real

> [!warning] Modo protegido raramente usado
> Memória acessada em segmentos de 64 KiB e **sem instrução para voltar ao modo real** — pouquíssimos SOs adotaram. O 286 geralmente funcionava como um 8086 mais rápido.

---

## Família 3 — 386

**Primeiro x86 de 32 bits.** Modo protegido de 32 bits usado até hoje. Tinha instrução para voltar ao modo real — viabilizou adoção real (Windows 3.0, 1990).

Novidades em relação ao 286:
- Registradores expandidos para **32 bits**
- Modo protegido de **32 bits** (acesso linear à memória, sem segmentação obrigatória)
- Modo **Virtual 8086** (V86)
- Acesso a até **4 GiB** (virtual + RAM)
- Paginação (memória virtual)
- Multitarefa por hardware

### 386DX vs 386SX

| Modelo | Barramento dados externo | Barramento endereços | Cache externo | Coprocessador |
|--------|------------------------|---------------------|---------------|--------------|
| **386DX** | 32 bits | 32 bits | Opcional na placa (64–128 KiB típico) | 387DX |
| **386SX** | 16 bits | 24 bits (→ max 16 MiB) | Não disponível | 387SX |

386SX usava memória e circuitos de apoio do 286 → mais barato; internamente idêntico ao DX, mas mais lento (barramento metade + sem cache).

### 486DLC e 486SLC (Cyrix/Texas Instruments)

Apesar do nome "486", são **família 3**:
- 486DLC = 386DX + 1 KiB cache L1
- 486SLC = 386SX + 1 KiB cache L1

Incluem as 6 novas instruções do 486, mas microarquitetura é de terceira geração.

---

## Família 4 — 486

Mantém tudo do 386 + integra FPU e cache L1 no chip.

Novidades principais:
- **FPU integrada** (antes: coprocessador externo 387) — exceto modelos SX
- **8 KiB de cache L1** interno (primeiro cache dentro do processador)
- Cache L2 externo opcional na placa (128–256 KiB típico)

### Variantes do 486

| Modelo | Destaque |
|--------|---------|
| **486DX** | Original |
| **486SX** | 486DX com FPU **desabilitada** → mais barato |
| **487SX** | "Coprocessador" para 486SX — na realidade um 486DX com pinos alterados; ao instalar, desabilita o 486SX e assume |
| **486DX2** | Primeiro x86 com **multiplicação de clock** (×2); ex: DX2-66 = 33 MHz externo × 2 = 66 MHz interno. Também vendido como OverDrive |
| **486SX2** | 486DX2 sem FPU |
| **486DX4** | Multiplicador ×3; cache L1 aumentado para **16 KiB** (Intel) ou 8 KiB (AMD/Cyrix) |

> [!note] 486DX2 — multiplicação de clock
> Esquema introduzido aqui e usado até hoje em todos os processadores modernos. Ver [[Gerenciamento de Consumo Elétrico]] — estados P também manipulam o multiplicador.

### 5x86

| Fabricante | O que era na realidade |
|-----------|----------------------|
| **AMD 5x86** | 486DX com L1 16 KiB + multiplicador ×4 ("486DX5") |
| **Cyrix 5x86** | Internamente um 6x86 (família 5) com pinagem de 486 |

---

## Ver também

- [[Processadores x86 Família 5]] — sucessores diretos: Pentium, K5, K6, 6x86
- [[Intel x86]] — visão geral da família x86 com linha do tempo condensada
- [[Modos de Operação x86]] — real / protegido 16/32 bits / V86 — introduzidos nestas famílias
- [[FPU]] — integrada ao 486; anteriormente coprocessadores 8087/287/387
- [[Cache]] — cache L1 introduzido no 486; cache externo (L2) existia desde o 386DX
- [[Fabricação de Chips]] — binning explica variantes DX/SX dentro de um mesmo wafer
- [[Gerenciamento de Consumo Elétrico]] — multiplicação de clock introduzida no DX2 é base dos estados P
