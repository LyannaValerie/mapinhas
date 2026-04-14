---
title: Instruções SIMD
tags:
  - hardware
  - arquitetura-x86
  - SIMD
  - instruções
aliases:
  - SIMD
  - MMX
  - SSE
  - AVX
---

# Instruções SIMD

**SIMD** (Single Instruction, Multiple Data) = divide registrador grande em vários menores e opera todos simultaneamente com uma única instrução. Roda na [[FPU]].

> [!important] Requisito duplo
> SIMD sozinho não ganha desempenho. Precisa: (1) processador com o conjunto; (2) programa compilado para usá-lo.

## Detecção via CPUID

Instrução `CPUID` retorna características do processador nos registradores de uso geral. Ferramentas como HWiNFO64 decodificam os resultados. Campo "Instructions" lista os conjuntos suportados.

## Exemplo: MMX somando 16 valores de 8 bits

Modo tradicional: 8 operações de soma separadas.  
Modo SIMD (MMX): carrega MM0 e MM1 com os 16 valores → `PADDB MM0,MM1` → resultados em MM2 (1 instrução).

---

## Conjuntos de Instruções

### MMX — 1996 (Intel, Pentium MMX)
- 47 novas instruções
- 8 registradores de 64 bits: **MM0–MM7** (aliases dos registradores R0–R7 da FPU; 16 bits mais significativos dos 80 bits originais ficam em "1")
- Acesso direto (ao contrário de R0–R7 que só acessam via pilha)
- Somente números inteiros
- Intel: Pentium MMX em diante. AMD: K6 em diante.

> [!note] MMX não é sigla oficialmente
> Intel registrou "MMX" como marca. Possíveis expansões: MultiMedia eXtension, Multiple Math eXtension, Matrix Math eXtension.

---

### 3DNow! — 1998 (AMD)
- 21 novas instruções
- AMD K6-2 em diante
- **Descontinuado:** AMD removeu suporte em agosto de 2010
- Intel nunca suportou

---

### SSE — 1999 (Intel, Pentium III)
- 70 novas instruções (inclui ponto flutuante — MMX só tem inteiros)
- 8 novos registradores de 128 bits: **XMM0–XMM7**; no modo 64 bits: **XMM8–XMM15**
- AMD: Athlon XP em diante
- Codinome pré-lançamento: **KNI** (Katmai New Instructions)

---

### SSE2 — 2000 (Intel, Pentium 4)
- 144 novas instruções
- Usa MM0–MM7 (64 bits) ou XMM0–XMM7 (128 bits)
- Somente inteiros (ponto flutuante SIMD veio no SSE3)
- AMD: Athlon 64 em diante

---

### SSE3 — 2004 (Intel, Pentium 4 HT / Prescott)
- 13 novas instruções
- AMD: Athlon 64 rev. E (Venice/San Diego) em diante
- Codinome: **PNI** (Prescott New Instructions)

---

### SSSE3 — 2006 (Intel, microarquitetura Core)
- 32 novas instruções
- AMD: Bobcat e Bulldozer (2011) em diante
- Codinomes: TNI (Tejas) / MNI (Merom)

---

### SSE4A — 2007 (AMD, Phenom)
- 4 novas instruções
- **Exclusivo AMD** — Intel nunca suportou
- ≠ SSE4 (subconjunto próprio da AMD)

---

### SSE4.1 — 2007 (Intel, Enhanced Core / Penryn)
- 47 novas instruções
- AMD: Bobcat e Bulldozer (2011) em diante

---

### SSE4.2 — 2008 (Intel, Nehalem)
- 7 novas instruções
- AMD: Bobcat e Bulldozer (2011) em diante

---

### AVX — 2011 (Intel Sandy Bridge / AMD Bobcat+Bulldozer)
- 12 novas instruções
- Expande XMM (128 bits) → **YMM (256 bits)**
- Origem: resposta da Intel à proposta SSE5 da AMD

---

### FMA
Instruções de adição e multiplicação fundidas (Fused Multiply-Add).

| Variante | Origem | Lançamento | Processadores |
|----------|--------|------------|---------------|
| **FMA4** | AMD | 2011 (Bulldozer) | AMD somente; removida no Zen |
| **FMA3** | Intel | 2013 (Haswell) | Intel Haswell+; AMD Piledriver+ (2012) |

> [!warning] Incompatibilidade
> FMA3 e FMA4 são conceitualmente iguais mas **incompatíveis**. Intel nunca suportou FMA4. Curiosamente, AMD suportou FMA3 antes da Intel (Piledriver, 2012 vs. Haswell, 2013).

---

### XOP — AMD (Bulldozer, 2011)
- eXtended OPerations — proposta do SSE5 da AMD
- Removida no Zen (assim como FMA4)
- Intel nunca suportou

---

### AVX2 — 2013 (Intel Haswell / AMD Excavator+Zen, 2015)
- Principal novidade: registradores YMM (256 bits) passam a suportar **inteiros** (AVX usava YMM só para ponto flutuante)
- Codinome: **HNI** (Haswell New Instructions)

---

### AVX-512 — 2013 (Intel, servidores)
- Registradores **ZMM0–ZMM31** (512 bits)
- 16 novos registradores no modo 64 bits (ZMM16–ZMM31), acessíveis também como YMM16–31 e XMM16–31
- 8 registradores de máscara de opcode: **k0–k7**
- Dividido em subconjuntos; somente **AVX-512F (Foundation)** é obrigatório; demais são opcionais por modelo
- AMD: Zen 4 (2022) em diante

---

### AESNI — 2010 (Intel Westmere)
- 6 instruções (+ 1 não-oficial) para acelerar criptografia AES
- AMD: Bulldozer em diante

---

### SHANI — Intel (Goldmont / Sunny Cove)
- 7 instruções para acelerar SHA-1 e SHA-256
- **Exclusivo Intel**

---

### AMX — 2022 (Intel Golden Cove / Sapphire Rapids, servidores)
- Advanced Matrix eXtensions — foco em IA e machine learning
- 2 componentes:
  - **Tiles**: 8 registradores 2D (tmm0–tmm7), cada um 16 linhas × 64 bytes = 1 KiB → total 8 KiB
  - **Acelerador TMUL** (Tile Matrix MULtiply): executa FMA sobre tiles
- Expansível: novos aceleradores podem ser adicionados futuramente

---

## Tabela Comparativa

| Conjunto | Ano | Origem | Registradores | Bits | Novas instruções |
|----------|-----|--------|---------------|------|-----------------|
| MMX | 1996 | Intel | MM0–MM7 | 64 | 47 |
| 3DNow! | 1998 | AMD | MM0–MM7 | 64 | 21 |
| SSE | 1999 | Intel | XMM0–XMM15 | 128 | 70 |
| SSE2 | 2000 | Intel | XMM | 128 | 144 |
| SSE3 | 2004 | Intel | XMM | 128 | 13 |
| SSSE3 | 2006 | Intel | XMM | 128 | 32 |
| SSE4A | 2007 | AMD | XMM | 128 | 4 |
| SSE4.1 | 2007 | Intel | XMM | 128 | 47 |
| SSE4.2 | 2008 | Intel | XMM | 128 | 7 |
| AVX | 2011 | Intel/AMD | YMM0–YMM15 | 256 | 12 |
| FMA3/4 | 2011–13 | AMD/Intel | YMM | 256 | — |
| AVX2 | 2013 | Intel | YMM | 256 | — |
| AVX-512 | 2013 | Intel | ZMM0–ZMM31 | 512 | — |
| AESNI | 2010 | Intel | XMM | 128 | 6 |
| AMX | 2022 | Intel | tmm0–tmm7 | tiles | — |

## Ver também

- [[FPU]] — registradores MMX são aliases dos registradores R0–R7 da FPU
- [[Registradores x86]] — hierarquia XMM/YMM/ZMM
- [[Concorrência e Paralelismo]] — SIMD como forma de paralelismo de dados
