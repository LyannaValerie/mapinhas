---
title: Processadores AMD — Família Bobcat (14h/16h)
tags:
  - hardware
  - arquitetura-x86
  - história
  - processadores
  - amd
  - baixo-consumo
aliases:
  - Bobcat
  - Jaguar
  - Puma
  - família 14h AMD
  - família 16h AMD
  - AM1
---

# Processadores AMD — Família Bobcat (14h/16h)

Período: **2011–2015**. Família voltada a **baixo consumo, baixo custo e baixa dissipação térmica**.  
Concorrente direto da família [[Microarquitetura Intel Família Atom|Intel Atom]].  
AMD encerrou esta família em **2015** — assim como Intel (2016) e NVIDIA (2014) abandonaram x86 para smartphones/tablets por falta de market share frente a Apple, Qualcomm, MediaTek e Samsung.

> [!note] Nomes de felinos
> Bobcat (lince) → Jaguar (onça) → Puma (suçuarana).

> [!note] Consoles de videogame
> PS4 e Xbox One usam microarquitetura **Jaguar**.  
> (O livro menciona "Xbox 360" — provável erro tipográfico; Xbox 360 usa IBM PowerPC Xenon.)

## Tabela de Identificação CPUID

| Microarquitetura | Família | Modelos |
|-----------------|---------|---------|
| Bobcat | 14h | 00h–0Fh |
| Jaguar | 16h | 00h–0Fh |
| Puma | 16h | 30h–3Fh |

---

## Bobcat vs. Bulldozer vs. Atom (2011)

| Aspecto | Bobcat | Bulldozer | Atom (Bonnell) |
|---------|--------|-----------|----------------|
| Arquitetura base | Similar a Hammer | Módulo compartilhado | Em ordem |
| Execução | Fora de ordem | Fora de ordem | **Em ordem** |
| Foco | Baixo consumo | Desktop/servidor | Baixo consumo |

> [!note] Vantagem Bobcat 2011
> Quando Bobcat foi lançado (2011), os Atom ainda executavam em ordem. Bobcat tinha vantagem de desempenho por ser OoO. A Intel corrigiu isso no Silvermont (2013). Ver [[Microarquitetura Intel Família Atom]].

---

## Microarquitetura Bobcat

### Conceito

- Similar à família Hammer; **sem módulos** da família Bulldozer
- Macroinstruções empacotadas em **grupos de 2** (K8 usa 3; Bulldozer usa 4)
- **2 portas de despacho** por tipo de instrução (INT, MEM, FP)
- Economia de energia: **power gating** (desliga alimentação) e **clock gating** (corta clock) em unidades ociosas; núcleos não utilizados podem ser completamente desligados
- **Cache L2 opera a clock/2** para reduzir consumo/dissipação térmica
- Todas as estruturas são de **64 bits** → operações de 128 bits = 2 macroinstruções

### Características Gerais

- L1-I: **32 KiB**, 2-way
- L1-D: **32 KiB**, 8-way
- L2: **512 KiB**, 16-way, acesso 128 bits, **clock/2**
- Instruções: SSE, SSE2, SSE3, SSSE3 (**sem FMA3, FMA4, TBM**)
- Virtualização suportada
- Turbo Core em alguns modelos
- Controlador de memória: **DDR3 canal único**
- PCIe **2.0 x4** para GPU discreta + PCIe **1.1 x4** UMI → chipset (**1 GB/s**)
- iGPU **DirectX 11** (TeraScale 2, codinome Evergreen)
- **1 ou 2 núcleos**

### Pipeline (Bobcat)

- 32 bytes lidos do L1-I → fila de **12 entradas de 16 bytes**
- Até **2 instruções x86 decodificadas por ciclo**
- Tipos de instrução: DirectPath Single, DirectPath Double, VectorPath (igual K8)
- Bits de pré-decodificação **não gravados de volta no cache** (diferença vs. Hammer)
- AMD divulgou diagrama de estágios mas **não detalhou função de cada estágio**

Estruturas internas (ver Figura 31.2):

| Estrutura | Tamanho |
|-----------|---------|
| Fila de busca | 12 × 16 bytes |
| ROB | **56 entradas** |
| Fila de retirada | 44 entradas |
| Agendador INT | 16 entradas |
| Agendador FP | 18 entradas |
| Agendador MEM | 8 entradas |
| PRF INT | 64 entradas |
| PRF FP | 88 entradas (**64 bits** cada) |

### Unidades de Execução (Bobcat)

**Inteiros:**

| Unidade | Função |
|---------|--------|
| ALU 0, ALU 1 | Aritmética/lógica |
| LAGU | Load Address Generation Unit — endereços de **carga** |
| SAGU | Store Address Generation Unit — endereços de **armazenamento** |
| MUL | Multiplicação de inteiros |

**Ponto flutuante (todas 64 bits):**

| Unidade | Função |
|---------|--------|
| MMX × 2 | Instruções x87 e SIMD |
| FPLOG × 2 | Instruções lógicas |
| IMUL | Multiplicação inteira x87/SIMD |
| STCONV | Armazenamento + conversão |
| FPADD | Soma FP |
| FPMUL | Multiplicação, divisão, raiz quadrada FP |

> [!note] L2 a 128 bits mas clock/2
> L1-D acessado a **64 bits** por ciclo. L2 acessado a **128 bits** mas em **clock/2** → largura de banda efetiva equivalente a 64 bits em clock cheio.

---

## Microarquitetura Jaguar

Funciona idêntico ao Bobcat com os seguintes aprimoramentos:

### Estruturas Internas

| Estrutura | Bobcat | Jaguar |
|-----------|--------|--------|
| Fila de busca | 12 entradas | **16 entradas** |
| ROB | 56 | **64** |
| Agendador INT | 16 | **20** |
| Agendador MEM | 8 | **12** |
| PRF FP | 88 × 64 bits | **72 × 128 bits** |
| Largura interna | 64 bits | **128 bits** |
| L1-D acesso | 64 bits | **128 bits** |

> [!note] PRF FP: menos entradas, mais largura
> 88 registradores de 64 bits → 72 de 128 bits. Registradores SSE de 128 bits deixam de precisar de 2 registradores físicos. Agora são instruções AVX de 256 bits que usam 2 registradores físicos.

### Outras Novidades do Jaguar

- Endereçamento físico estendido para **40 bits**
- L2: **1 MiB** (2 núcleos) ou **2 MiB** (4 núcleos), **compartilhado**, 128 bits a clock/2
- 256-bit ops = 2 macroinstruções (antes eram 128-bit = 2 macros)
- Instruções: +**SSE4.1, SSE4.2, AVX, F16C, BMI1, AES-NI, CLMUL**; ver [[Instruções SIMD]]
- iGPU DX11 (GCN2, **UVD 4.0**, VCE 2.0)
- USB integrado: **6× USB 2.0 + 4× USB 3.2 Gen 1x1**
- SATA-600: **2 portas**
- PCIe **2.0 x4** (GPU) + PCIe **2.0 x4** UMI (**2 GB/s**, antes PCIe 1.1 = 1 GB/s)
- Socket desktop: **AM1** (incompatível com qualquer outro soquete)
- **2 ou 4 núcleos**

### Pipeline (Jaguar)

Adicionados 2 estágios vs. Bobcat:
- **IDEC**: acredita-se ser tratamento de instruções de inteiros
- Estágio adicional de renomeamento de registradores FP

Motivação: mais estágios = menos portas lógicas por estágio = clocks mais altos possíveis (mesmo princípio do Netburst).

### Unidades de Execução (Jaguar)

**Inteiros:** igual Bobcat + **DIV** (divisão inteira adicionada).

**Ponto flutuante (todas 128 bits):**

| Jaguar | Equivalente Bobcat | Função |
|--------|-------------------|--------|
| VALU0, VALU1 | MMX×2 + FPLOG×2 (fusão) | Lógica e aritmética SIMD vetorial (AVX) |
| VIMUL | IMUL | Multiplicação inteira vetorial |
| STCONV | STCONV | Armazenamento + conversão |
| FPADD | FPADD | Soma FP |
| FPMUL | FPMUL | Multiplicação, divisão, raiz quadrada FP |

---

## Microarquitetura Puma

Jaguar com adições mínimas:

- Instrução **RDRAND**; ver [[Instruções Adicionais x86]]
- **PSP** (Platform Security Processor): RNG + **TPM 2.0** (mesmo sistema do [[Processadores AMD Família Bulldozer|Excavator]])
- UVD **4.0 → 4.2**

### Puma+

Apenas atualização de codecs de vídeo: UVD **6.0** + VCE **3.1**. Sem outra diferença.

---

## Processadores

> [!warning] Nomes reutilizados em múltiplas µarqs
> "Série A", "Série E", "Athlon", "Sempron" e "Opteron" foram usados tanto nesta família quanto em outras (K10.5, Bulldozer). Sempre verificar CPUID ou codinome.

### Athlon / Sempron (Desktop, AM1)

| Codinome | Ano | Processo | µarq | Núcleos | L2 | Turbo Core |
|---------|-----|---------|------|---------|----|-----------|
| Kabini | 2013 | 28 nm | Jaguar | 2 (Sempron), 4 (Sempron/Athlon) | 1 MiB (2c) / 2 MiB (4c) | Não |

Athlon X4 → rebrandado para Athlon após alguns anos.

### Série A (Portátil, soldered)

| Codinome | Ano | Processo | µarq | Núcleos | L2 | Turbo Core |
|---------|-----|---------|------|---------|----|-----------|
| Kabini | 2013 | 28 nm | Jaguar | 4 | 2 MiB | Não |
| Temash | 2014 | 28 nm | Jaguar | 2/4 | 1/2 MiB | Apenas A6 |
| Mullins | 2014 | 28 nm | Puma | 4 | 2 MiB | Sim |
| Beema | 2014 | 28 nm | Puma | 4 | 2 MiB | A4 Pro/A6/A8 |
| Carrizo-L | 2015 | 28 nm | Puma+ | 4 | 2 MiB | Sim |

### Série E (Portátil)

| Codinome | Ano | Processo | µarq | Núcleos | L2 | Turbo Core |
|---------|-----|---------|------|---------|----|-----------|
| Zacate | 2011 | 40 nm | Bobcat | 1/2 | — | Não |
| Kabini | 2013 | 28 nm | Jaguar | 2/4 | 1/2 MiB | Não |
| Mullins | 2014 | 28 nm | Puma | 2 | 1 MiB | Sim |
| Beema | 2014 | 28 nm | Puma | 2/4 | 1/2 MiB | Não |
| Carrizo-L | 2015 | 28 nm | Puma+ | 2/4 | 1/2 MiB | Não |

### Série C (Portátil)

| Codinome | Ano | Processo | µarq | Núcleos | Turbo Core |
|---------|-----|---------|------|---------|-----------|
| Ontario | 2011 | 40 nm | Bobcat | 1/2 | Apenas C-60/C-70 |

### Série Z (Tablets)

| Codinome | Modelo | Ano | Processo | µarq | Núcleos |
|---------|--------|-----|---------|------|---------|
| Desna | Z-01 | 2011 | 40 nm | Bobcat | 2 |
| Hondo | Z-60 | 2012 | 40 nm | Bobcat | 2 |

### Opteron (Jaguar)

| Codinome | Ano | Processo | µarq | Núcleos | L2 | Soquete |
|---------|-----|---------|------|---------|-----|---------|
| Kyoto | 2013 | 28 nm | Jaguar | 4 | 2 MiB | FT3 (BGA) |

---

## Tabela Comparativa µarqs Bobcat

| Recurso | Bobcat | Jaguar | Puma |
|---------|--------|--------|------|
| CPUID família | 14h | 16h | 16h |
| Largura interna | **64 bits** | **128 bits** | 128 bits |
| L1-D acesso | 64 bits | **128 bits** | 128 bits |
| L1-I | 32 KiB | 32 KiB | 32 KiB |
| L1-D | 32 KiB | 32 KiB | 32 KiB |
| L2 | 512 KiB | 1/2 MiB (2/4c) | 1/2 MiB |
| L2 compartilhado | Não | **Sim** | Sim |
| ROB | 56 | **64** | 64 |
| PRF FP | 88 × 64b | **72 × 128b** | 72 × 128b |
| Instruções | SSE–SSSE3 | +**AVX, SSE4.x, AES-NI** | +**RDRAND** |
| PSP/TPM | Não | Não | **Sim** |
| iGPU | DX11 (TeraScale 2) | **DX11 (GCN2)** | GCN2 |
| UMI | PCIe 1.1 x4 (1 GB/s) | **PCIe 2.0 x4 (2 GB/s)** | 2.0 |
| Socket desktop | — | **AM1** | — |
| Macros por linha | 2 | 2 | 2 |
| Processo | 40 nm | **28 nm** | 28 nm |

---

## Ver também

- [[Processadores AMD Família Bulldozer]] — família contemporânea para desktop/servidor
- [[Processadores AMD Família Hammer]] — predecessores; Série E K10.5 ≠ Bobcat (ver warning naquela nota)
- [[Microarquitetura Intel Família Atom]] — concorrente direto; Atom foi em ordem até 2013
- [[Instruções SIMD]] — SSE4.1/4.2, AVX, AES-NI, F16C, BMI1 no Jaguar
- [[Instruções Adicionais x86]] — RDRAND, PSP, TPM 2.0 no Puma
- [[Cache]] — L2 a clock/2 para economia de energia; L2 compartilhado no Jaguar
- [[Desempenho do Processador]] — power gating, clock gating, execução OoO em baixo consumo
- [[Fabricação de Chips]] — 40 nm (Bobcat) → 28 nm (Jaguar/Puma)
