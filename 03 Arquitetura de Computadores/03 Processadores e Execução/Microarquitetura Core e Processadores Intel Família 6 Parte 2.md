---
title: Microarquitetura Core — Intel Família 6 (Parte 2)
tags:
  - hardware
  - arquitetura-x86
  - história
  - processadores
  - intel
aliases:
  - microarquitetura Core
  - microarquitetura Banias
  - microarquitetura Enhanced Core
  - Penryn
  - Pentium M
  - Core Solo
  - Core Duo
  - Core 2
  - Centrino
  - família 6 Intel parte 2
  - LSD
  - Advanced Digital Media Boost
  - Smart Memory Access
  - Advanced Smart Cache
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Microarquitetura Core — Intel Família 6 (Parte 2)

Período: **2003–2008**. Processadores: Pentium M, Core Solo, Core Duo, Core 2 e derivados.

> [!important] Retorno à eficiência
> Após o fracasso térmico do Netburst, a Intel retomou a linhagem P6 evoluída. A microarquitetura Core foi desenvolvida em Israel a partir do Pentium M e tornou-se a base de todas as microarquiteturas Intel posteriores (Nehalem, Sandy Bridge…).

---

## Modelo Tick-Tock

Adotado pela Intel a partir de **2006** (introdução da microarquitetura Core):

| Fase | O que muda | Exemplo |
|------|-----------|---------|
| **Tock** | Nova microarquitetura + novo processo | Core em 65 nm |
| **Tick** | Mesmo núcleo, processo menor | Enhanced Core (Penryn) em 45 nm |

> [!note] Tick-Tock vigorou 2006–2015. Ver [[Microarquitetura Netburst e Intel Família F]] para contexto do fracasso que motivou a mudança.

---

## Microarquitetura Banias (2003)

Base: [[Microarquitetura P6 e Processadores Intel Família 6 Parte 1]] + melhorias focadas em portáteis.

| Atributo | Valor |
|----------|-------|
| Origem | P6 + Pentium III + modificações |
| L1 | 32 KiB (16+16) |
| FSB | QDR (como Netburst) |
| Fusão de µops | Sim — instruções x86 podem fundir em 1 µop |
| SpeedStep | Sim (foco em eficiência energética) |
| SSE2 | Sim |
| SSE3 | Não (adicionado na revisão Dothan) |
| 64 bits | **Não** |

> [!warning] Banias sem 64 bits
> Toda a linha Pentium M (Banias/Dothan) opera apenas em modo de 32 bits. Intel adicionou suporte a 64 bits (EM64T) a partir da microarquitetura Core (Yonah revisado) e confirmado plenamente no Core 2.

---

## Microarquitetura Core (2006)

Codinome Yonah → Merom/Conroe/Woodcrest. Base do Core Solo, Core Duo e Core 2.

### Pipeline Core (14 estágios)

```
BUSCA (×4)
→ DECODIFICAÇÃO (×4)
→ RENOMEAMENTO / ALOCAÇÃO
→ BUFFER DE REORDENAMENTO (ROB)
→ ENVIO (fora de ordem)
→ EXECUÇÃO (fora de ordem)
→ RETIRADA (em ordem)
```

Dois estágios a mais que a P6 original (12) → mais instruções em paralelo.

### Loop Stream Detector (LSD)

- Buffer de **18 µops** na fila IDQ
- Detecta loops curtos (≤ 18 µops) **já decodificados**
- Loop identificado → µops reusadas do buffer sem re-decodificar
- Reduz atividade do decodificador em loops → economia de energia

> [!tip] LSD é predecessor funcional do DSB (Decoded Stream Buffer) do Sandy Bridge.

### Decodificadores

| Tipo | Qtd | Produz |
|------|-----|--------|
| Simples | 3 | 1 µop cada |
| Complexo | 1 | 1–4 µops |
| Microcódigo (MSROM) | via complexo | >4 µops |

Saída: até **4 µops/ciclo** (vs. 3 na P6).

### Fusão de Instruções

| Tipo | O que faz | Exemplo |
|------|-----------|---------|
| **Macro-fusão** | 2 instruções x86 → 1 µop no decodificador | `CMP` + `JCC` fundidas |
| **Micro-fusão** | 2 µops internas → 1 µop no ROB | load + operação aritmética |

Ambas reduzem pressão no ROB e na estação de reserva.

### Execução Fora de Ordem

| Estrutura | Core |
|-----------|------|
| ROB | 96 µops |
| Estação de Reserva | 32 µops |
| Portas de despacho | 6 |

### Unidades de Execução — Core (Merom/Conroe)

| Unidade | Porta | Pipeline? | Função |
|---------|-------|-----------|--------|
| ALU | 0, 1 | Não | Inteiros gerais |
| INT MUL / SHIFT | 1 | Sim | Multiplicação inteira, rotações |
| LEA | 0 | Não | Load Effective Address |
| Desvios | 0 | — | Desvios + limpeza ROB |
| SSE ALU (×2) | 0, 1 | Sim | SIMD inteiros e FP |
| SSE MUL | 0 | Sim | SIMD multiplicação FP |
| SSE SHUFFLE | 1 | Não | SIMD shuffles, moves |
| AGU carga | 2 | Sim | Endereço de leitura |
| AGU armazenamento | 3 (end.) + 4 (dados) | Sim | Endereço e dado de escrita |

### Advanced Digital Media Boost

- Caminhos de dados SSE expandidos para **128 bits nativos**
- P6/Pentium III: SSE 128 bits = 2 µops de 64 bits
- Core: SSE 128 bits = **1 µop** (throughput 2× em operações SIMD)

### Smart Memory Access (SMA)

- Técnica de **desambiguação de memória** (memory disambiguation)
- Prevê se uma carga pode ser executada antes de uma armazenamento anterior completar
- Reduz travamentos de dependência carga/armazenamento

### Advanced Smart Cache

- L2 **compartilhado** entre núcleos no dual-core
- Cada núcleo acessa cache L2 do outro sem sair do chip
- Implementações possíveis:

| Modelo | Descrição |
|--------|-----------|
| **Monolítico** | 1 die com 2 núcleos + L2 compartilhado |
| **Multichip (MCM)** | 2 dies com L2 independentes no mesmo pacote |
| **Híbrido** | 2 dies + L2 separados + L2 compartilhado externo |

### Instruções e Recursos Novos

| Recurso | Disponível |
|---------|-----------|
| SSE3 | Sim |
| SSSE3 | Sim (Core) |
| 64 bits (EM64T) | Sim |
| VT-x | Sim (alguns modelos) |
| TXT/SMX | Sim (alguns modelos) |

---

## Microarquitetura Enhanced Core / Penryn (2007–2008)

**Tick** do modelo tick-tock: mesma microarquitetura Core em processo de **45 nm**.

### Novidades em relação à Core

| Recurso | Detalhe |
|---------|---------|
| Processo | 65 nm → **45 nm** |
| SSE4.1 | Adicionado (47 instruções) |
| Radix-16 DIV | Divisão FP e inteira mais rápida (menos ciclos) |
| SSE SHUFFLE | Agora com **pipeline** (era sem pipeline no Core) |
| L2 | Maior (até 6 MiB em alguns modelos) |

### Unidades de Execução — Penryn

Iguais ao Core, com a diferença no SSE SHUFFLE:

| Unidade | Mudança vs. Core |
|---------|-----------------|
| SSE SHUFFLE | Passa a ter **pipeline** |
| FP DIV | Radix-16: latência reduzida |

---

## Processadores

### Pentium M — 2003–2006 (Banias / Dothan)

- **Plataforma Centrino** = Pentium M + chipset 855 + Wi-Fi Intel PRO/Wireless
- Banias: 130 nm, L2 1 MiB
- Dothan: 90 nm, L2 2 MiB, SSE3

> [!warning] Pentium M ≠ Pentium 4-M
> Pentium M = família 6 (Banias/Dothan). Pentium 4-M = Netburst para portáteis. Nomes semelhantes, microarquiteturas completamente diferentes.

### Core Solo / Core Duo — 2006 (soquete PPGA478 / M / rPGA478)

| Modelo | Núcleos | Codinome | L2 |
|--------|---------|----------|----|
| Core Solo | 1 | Yonah | 2 MiB |
| Core Duo | 2 | Yonah | 2 MiB compartilhado |

- Baseados em microarquitetura Core (Yonah)
- Portáteis e All-in-Ones (iMac primeira geração)
- **Sem 64 bits** (Yonah não tem EM64T)

### Core 2 Duo — 2006 (LGA775 / socket P)

| Codinome | Processo | L2 | Destaque |
|---------|----------|----|---------|
| Conroe | 65 nm | 2/4 MiB | Desktop |
| Merom | 65 nm | 2/4 MiB | Mobile |
| Allendale | 65 nm | 2 MiB | Versão econômica |

- **Com 64 bits** (EM64T)
- VT-x em alguns modelos

### Core 2 Solo — 2007

- Merom de 1 núcleo para portáteis

### Core 2 Quad — 2007 (LGA775)

| Codinome | Processo | L2 | Implementação |
|---------|----------|-----|--------------|
| Kentsfield | 65 nm | 2×4 MiB | **2 dies Conroe** no mesmo pacote (MCM) |
| Yorkfield | 45 nm | 2×6 MiB | **2 dies Wolfdale** no mesmo pacote (MCM) |

> [!note] Core 2 Quad = MCM, não monolítico
> Dois dies independentes no mesmo pacote. L2 dos núcleos não é compartilhado entre os dois dies — comunicação entre dies passa pelo FSB. Ver Advanced Smart Cache.

### Core 2 Extreme — 2006–2008

- Versão entusiasta: multiplicador destravado (overclock)
- Inclui Core 2 Duo Extreme (2 núcleos) e Core 2 Quad Extreme (4 núcleos)

### Pentium Dual Core → Pentium — 2007

- Core 2 Duo com L2 reduzido (1 MiB) → versão econômica
- Renomeado de "Pentium Dual Core" para "Pentium" em 2009

### Celeron (Core / Penryn)

- Versão de baixo custo do Core 2: L2 ainda mais reduzido (512 KiB)
- **Atenção:** nome Celeron usado por múltiplas microarquiteturas ao longo dos anos

### Celeron M

- Celeron para portáteis baseado em Banias/Dothan ou Yonah

### Xeon (Core / Penryn) — LGA771 / LGA775

| Codinome | Processo | Núcleos | L2 | Destaque |
|---------|----------|---------|-----|---------|
| Woodcrest | 65 nm | 2 | 4 MiB | SMP 2 vias |
| Clovertown | 65 nm | 4 (MCM) | 2×4 MiB | SMP 2 vias (2 dies) |
| Harpertown | 45 nm | 4 (MCM) | 2×6 MiB | Penryn, SMP 2 vias |

### Xeon MP (Core / Penryn) — SMP 4 e 8 vias

| Codinome | Processo | Núcleos | L3 |
|---------|----------|---------|-----|
| Tigerton | 65 nm | 4 (MCM) | — |
| Dunnington | 45 nm | 6 (monolítico) | 16 MiB |

> [!note] Dunnington
> Primeiro Xeon Intel com L3 integrado no die. 6 núcleos em die único = arquitetura monolítica (não MCM).

---

## Ver também

- [[Microarquitetura Intel Família 6 Parte 3]] — sucessores: Nehalem→Golden Cove, Core i, DSB, PRF, QPI/DMI/UPI
- [[Microarquitetura P6 e Processadores Intel Família 6 Parte 1]] — predecessores (Pentium Pro/II/III); ROB/RS/portas tabela base
- [[Microarquitetura Netburst e Intel Família F]] — fracasso que motivou o retorno à linhagem P6
- [[Desempenho do Processador]] — fusão de µops, LSD, execução OoO, SMT/HT
- [[Cache]] — Advanced Smart Cache (L2 compartilhado); MCM vs monolítico
- [[Instruções SIMD]] — SSSE3 (Core), SSE4.1 (Penryn)
- [[Gerenciamento de Consumo Elétrico]] — SpeedStep no Pentium M; tick-tock e redução de processo
- [[Máquina Virtual]] — VT-x presente em alguns modelos Core
- [[Instruções Adicionais x86]] — TXT/SMX em alguns modelos Core
- [[Fabricação de Chips]] — tick-tock: mesma µarq em nó menor (65→45 nm)
