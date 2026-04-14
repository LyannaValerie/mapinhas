---
title: Processadores AMD — Família K7
tags:
  - hardware
  - arquitetura-x86
  - história
  - processadores
  - amd
aliases:
  - K7
  - Athlon
  - Athlon XP
  - Duron
  - Sempron K7
  - Athlon MP
  - soquete A
  - soquete 462
  - família 6 AMD
---

# Processadores AMD — Família K7

Período: **1999–2004**. CPUID família = **6** (igual à família 6 Intel — lógica diferente, ver abaixo).

> [!note] Por que família 6?
> K5 e K6 respondem como família 5 por usarem o mesmo soquete/plataforma dos processadores família 5 da Intel. K7 é a 7ª geração AMD, mas responde como família 6 pela mesma lógica de compatibilidade de plataforma. Não há correspondência direta entre contagens de geração Intel e AMD.

---

## Contexto

- Concorria com Pentium II/III (família 6 Intel) e primeiros Pentium 4 (família F Intel)
- Microarquitetura K7: CISC/RISC híbrido, OoO, especulativa, renomeamento de registradores, superescalar — solidificou essas técnicas na AMD
- Barramento externo DDR: **2 dados/pulso de clock** → AMD divulga clock duplicado (ex.: "200 MHz" = 100 MHz real → escrever como **200 MT/s**)

---

## Barramento Externo DDR (notação)

| Notação AMD | Clock real | Notação correta |
|-------------|-----------|----------------|
| "200 MHz" | 100 MHz | **200 MT/s** |
| "266 MHz" | 133 MHz | **266 MT/s** |
| "333 MHz" | 166 MHz | **333 MT/s** |
| "400 MHz" | 200 MHz | **400 MT/s** |

> [!warning] Mesma armadilha do FSB QDR da Intel
> Intel infla o clock do FSB Netburst (QDR = ×4). AMD infla ×2 (DDR). Ambos devem ser expressos em MT/s. Ver [[Microarquitetura Netburst e Intel Família F]].

---

## Nomenclatura PR (Performance Reference)

Usada no Athlon XP, Athlon XP-M, Athlon MP e Sempron K7.

- Número no nome **não é o clock real** — é referência ao clock do concorrente Intel equivalente
- Ex.: Athlon XP **2000+** roda a **1,6 GHz** (não 2 GHz)
- Justificativa da AMD: clock não é sinônimo de desempenho entre µarqs distintas — processador com clock menor pode ser mais rápido se tiver mais unidades de execução ou CPI menor
- Duron: usava clock real (sem PR)

---

## Processadores

### Athlon — 1999–2001

Primeiro processador K7. Concorrente direto do Pentium II e Pentium III.

- **L1:** 64 KiB I + 64 KiB D
- **Instruções:** MMX, Enhanced 3DNow! (sem SSE ainda)
- **Slot A** (primeiros modelos, cartucho similar ao Slot 1 da Intel mas incompatível) → depois **soquete 462 (soquete A)**

| Codinome | Processo | Modelo CPUID | L2 | FSB | Slot/Soquete |
|---------|----------|-------------|-----|-----|-------------|
| Argon | 250 nm | 1 | 512 KiB (1:2) | 200 MT/s | Slot A |
| Pluto | 180 nm | 2 | 512 KiB (1:2, 2:5 ou 1:3) | 200 MT/s | Slot A |
| Orion | 180 nm | 2 | 512 KiB (2:5 ou 1:3) | 200 MT/s | Slot A |
| Thunderbird | 180 nm | 4 | **256 KiB (1:1)** | 200 ou 266 MT/s | Slot A ou Soquete 462 |

Notação da relação L2/clock: 1:2 = metade do clock interno; 1:1 = mesmo clock.

---

### Athlon XP — 2001–2004

Athlon com SSE adicionado. Nomenclatura **PR**. Todos no **soquete 462**. L2 sempre **1:1** (mesmo clock interno).

Conjunto de instruções SIMD completo: MMX + 3DNow! + Enhanced 3DNow! + SSE → AMD chamou de **3DNow! Professional**.

| Codinome | Processo | Modelo CPUID | L2 | FSB |
|---------|----------|-------------|-----|-----|
| Palomino | 180 nm | 6 | 256 KiB | 266 MT/s |
| Thoroughbred | 130 nm | 8 | 256 KiB | 266/333/400 MT/s |
| Thorton | 130 nm | 10 | 256 KiB | 266/333/400 MT/s |
| Barton | 130 nm | 10 | **512 KiB** | 266/333/400 MT/s |

> [!note] Athlon XP → Athlon 4 → Athlon XP
> Lançado inicialmente como "Athlon 4" (alusão ao Pentium 4), renomeado para "Athlon XP" logo depois. Nome "Athlon 4" mantido em alguns modelos portáteis.

---

### Duron — 2000–2003

Versão de baixo custo do Athlon/Athlon XP: L2 reduzido, FSB menor. **Sem nomenclatura PR** — usa clock real.

| Codinome | Processo | Modelo CPUID | L2 | FSB | SSE |
|---------|----------|-------------|-----|-----|-----|
| Spitfire | 180 nm | 3 | 64 KiB | 200 MT/s | Não |
| Morgan | 180 nm | 7 | 64 KiB | 200 MT/s | Sim |
| Applebred | 130 nm | 8 | 64 KiB | 266 MT/s | Sim |

Todos no **soquete 462**. L2 a 1:1 (mesmo clock interno).

---

### Sempron (K7) — 2004

Athlon XP renomeado para estender vida da plataforma após lançamento do Athlon 64. Nomenclatura PR.

> [!warning] Sempron K7 ≠ Sempron K8
> AMD lançou duas versões simultâneas do Sempron: uma baseada em K7 (este capítulo) e outra baseada em K8 (próximo capítulo). Mesmo nome, microarquiteturas diferentes.

Versões K7:

| Codinome | Processo | Modelo CPUID | L2 | FSB |
|---------|----------|-------------|-----|-----|
| Thoroughbred-B | 130 nm | 8 | 256 KiB | 333 MT/s |
| Thorton | 130 nm | 10 | 256 KiB | 333 MT/s |
| Barton | 130 nm | 10 | 256 KiB | 333 ou 400 MT/s |

---

### Mobile Athlon 4 — 2001–2002

Athlon XP Palomino para portáteis. FSB 200 MT/s. Mesmo soquete dos modelos desktop (soquete 462). Alguns modelos com PR, outros com clock real.

---

### Mobile Athlon XP — 2002–2003

Athlon XP Thoroughbred para portáteis. FSB 200 ou 266 MT/s. Soquete 462. Nomenclatura PR.

---

### Athlon XP-M — 2002–2004

Terceiro nome para Athlon XP portátil. Baseado no Thoroughbred ou Barton. FSB 266 MT/s.

Soquetes: **462** (mesmo do desktop) ou **563** (novo, exclusivo para portáteis).

> [!note] Dublin ≠ Athlon XP-M
> Uma versão "Dublin" do Athlon XP-M foi lançada, mas era baseada no **Athlon 64** (K8), não no K7.

---

### Mobile Duron — 2001–2002

Duron para portáteis de baixo custo. Soquete 462.

| Codinome | Processo | Modelo CPUID | L2 | FSB | SSE |
|---------|----------|-------------|-----|-----|-----|
| Spitfire | 180 nm | 3 | 64 KiB | 200 MT/s | Não |
| Camaro | 180 nm | 7 | 64 KiB | 200 MT/s | Sim |

---

### Athlon MP — 2001–2003

Athlon XP para servidores com **SMP até 2 processadores**. Soquete 462. Nomenclatura PR.

| Codinome | Processo | L2 | FSB |
|---------|----------|----|-----|
| Palomino | 180 nm | 256 KiB | 266 MT/s |
| Thoroughbred | 130 nm | 256 KiB | 266 MT/s |
| Barton | 130 nm | 512 KiB | 266 MT/s |

---

## Ver também

- [[Processadores x86 Família 5]] — predecessores AMD: K5, K6, K6-2, K6-III
- [[Processadores AMD Família Hammer]] — sucessores AMD: Athlon 64, Phenom, Opteron, Turion
- [[Microarquitetura P6 e Processadores Intel Família 6 Parte 1]] — concorrentes Intel diretos (Pentium II/III)
- [[Microarquitetura Netburst e Intel Família F]] — concorrentes Intel (Pentium 4); FSB QDR também usa notação inflada
- [[Desempenho do Processador]] — técnicas K7: OoO, especulativa, renomeamento, superescalar, CISC/RISC híbrido
- [[Instruções SIMD]] — MMX, 3DNow!, Enhanced 3DNow!, SSE (Athlon XP)
- [[Fabricação de Chips]] — evolução 250→180→130 nm nos codinomes K7
