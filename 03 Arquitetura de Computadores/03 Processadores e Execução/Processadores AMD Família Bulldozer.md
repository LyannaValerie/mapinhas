---
title: Processadores AMD — Família Bulldozer (15h)
tags:
  - hardware
  - arquitetura-x86
  - história
  - processadores
  - amd
aliases:
  - Bulldozer
  - Piledriver
  - Steamroller
  - Excavator
  - família 15h AMD
  - FX AMD
  - APU Série A
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Processadores AMD — Família Bulldozer (15h)

Período: **2011–2017**. Quatro microarquiteturas baseadas no conceito de **módulo** compartilhado.  
CPUID Família: **15h** (hex) = 21 decimal. Identificação da µarq pelo campo **Modelo**.

> [!note] Nomes de máquinas de construção civil
> Bulldozer (trator de esteira) → Piledriver (bate-estacas) → Steamroller (rolo compressor) → Excavator (escavadeira).

## Tabela de Identificação CPUID

| Microarquitetura | Família | Modelos |
|-----------------|---------|---------|
| Bulldozer | 15h | 00h–01h |
| Piledriver | 15h | 02h, 10h–1Fh |
| Steamroller | 15h | 30h–3Fh |
| Excavator | 15h | 60h–6Fh |
| Excavator+ | 15h | 70h–7Fh |

---

## Conceito de Módulo Bulldozer

Inovação central da família: organização em **módulos**, cada um composto por:

- **2 blocos de inteiros** (independentes)
- **1 bloco de ponto flutuante** (compartilhado entre os dois inteiros)
- **1 front-end compartilhado**: cache L1-I, unidade de busca, decodificador

O SO enxerga cada módulo como **2 processadores** (2 "núcleos"). Porém, cada módulo não equivale a 2 processadores completos — FP e front-end são compartilhados.

> [!warning] Polêmica: AMD processada judicialmente em 2015
> AMD foi processada por propaganda enganosa por divulgar cada módulo como tendo "dois núcleos". Do ponto de vista técnico, cada módulo fica entre um processador com 1 núcleo + HyperThreading e um verdadeiro dual-core, sendo superior à primeira abordagem, mas inferior à segunda.

---

## Microarquitetura Bulldozer (1ª geração)

### Pipeline

- Decodificador gera **4 macroinstruções/ciclo** (empacotadas em 1 linha de 4 µinstruções)
- Decodifica: 4 DirectPath Single OU 2 DirectPath Single + 1 DirectPath Double por ciclo
  - Limitação: **não decodifica 2 DirectPath Double simultaneamente** (removido no Piledriver)
- **Fusão de instruções**: compare + desvio condicional → 1 macroinstrução única; ver [[Instruções Adicionais x86]]
- Agendador INT: **40 entradas** (10 linhas × 4 µops); agendador FP: **64 entradas** (16 linhas × 4 µops)
- Renomeamento de registradores: técnica **PRF**; INT: 96 entradas; FP: 160 entradas
- L1-D: **2 portas de 128 bits** → carga/armazenamento de 256 bits por ciclo
- Estágios do pipeline: **não divulgados pela AMD**

### Unidades de Execução (Bulldozer)

**Por unidade de inteiros** (2 por módulo):

| Unidade | Função |
|---------|--------|
| ALU 0, ALU 1 | Aritmética/lógica de inteiros |
| AGU 0, AGU 1 | Cálculo de endereços |
| ABM | Bit manipulation (POPCNT/LZCNT) |
| MUL | Multiplicação de inteiros |
| Desvios | Apenas 1 porta por unidade de inteiros |

**Unidade de Ponto Flutuante** (1 por módulo, compartilhada):

| Unidade | Nome alternativo | Função |
|---------|-----------------|--------|
| FMAC × 2 | FPMA | Soma, multiplicação, divisão, raiz quadrada, FMA4 — **128 bits** |
| IMAC | FPMMA | Multiplicação e FMA de inteiros em MMX/SSE/AVX |
| MMX × 2 | FPMAL | Lógica e aritmética SIMD inteiros até 128 bits (executa MMX, SSE, AVX) |
| FPCVT | — | Conversão de ponto flutuante |
| XBAR | FPXBR | Permutação/rotação (shuffle, shift, pack/unpack) |
| FSTOR | FPSTO | Armazenamento em memória |

> [!note] Largura das unidades FP e instruções de 256 bits
> Todas as unidades FP são de **128 bits**. Instruções AVX de 256 bits são decodificadas como **2 macroinstruções** e executadas em 2 unidades combinadas. Armazenamento de 256 bits alinhado: **3 ciclos** (reduzido para 2 ciclos no Steamroller).

### Características Gerais — Bulldozer

- L1-I: **64 KiB** 2-way por módulo; TLB L1-I: 48 entradas
- L1-D: **16 KiB** 4-way por unidade de inteiros; TLB L1-D: 32 entradas
- L2: compartilhado por módulo (tamanho varia por modelo)
- L3: compartilhado entre todos os módulos
- Instruções: **FMA4, SSE4.1, SSE4.2, AVX, AES-NI**; ver [[Instruções SIMD]] e [[Instruções Adicionais x86]]
- Turbo Core **2ª geração**
- Controlador de memória: **DDR3 dual-channel**
- Interconexão: **HyperTransport 3.0** (servidores com múltiplos links)
- Socket desktop: **AM3+**; servidores: C32, G34

---

## Microarquitetura Piledriver (2ª geração)

Novidades em relação à Bulldozer:

- TLB L1-D: **32 → 64 entradas**
- L3 presente apenas em **FX AM3+ e Opteron** (removido nos demais)
- Decodificador: agora decodifica **2 DirectPath Double simultaneamente**
- Novas instruções: **F16C, FMA3, BMI1, TBM**
- Unidades INT: adicionadas **DIV** (divisão inteira) e **MOV** (movimentação de dados)
- Socket desktop: **FM2**
- PCIe **2.0** integrado (modelos FM2): 2×x8 (combináveis em x16) para vídeo, x4 UMI, x4 misc
- **UMI** substitui HyperTransport nos modelos FM2
- iGPU **DirectX 11** (TeraScale 3, codinome Northern Islands) em Série A e FirePro
- Turbo Core **3ª geração**

### Unidades de Execução (Piledriver)

INT: igual à Bulldozer + unidades **DIV** e **MOV** adicionadas.  
FP: idêntica à Bulldozer.

---

## Microarquitetura Steamroller (3ª geração)

Novidades em relação ao Piledriver:

- L1-I: **64 → 96 KiB**
- **L3 removido** em todos os modelos
- Busca: **32 bytes a cada 2 ciclos** (antes: 32 bytes/ciclo)
- **Segundo decodificador** adicionado — dois decodificadores paralelos, cada um gerando até 4 µops/ciclo
- **2 filas de instruções decodificadas** (40 entradas cada = até 40 µops em loop); equivalente ao [[Microarquitetura Intel Família 6 Parte 3|LSD Intel Nehalem]]
- Registradores físicos FP: **160 → 176 entradas**
- FP: **1 porta de despacho removida**; unidade MMX realocada; nova unidade **SHUFFLE** para permutação/rotação
- PCIe **3.0** para vídeo (2×x8), PCIe 2.0 x4 UMI, PCIe 2.0 x4 misc
- iGPU **DirectX 12** (GCN2, codinome Bonaire)
- Socket: **FM2+**
- **Sem modelos HyperTransport**

### Unidades de Execução (Steamroller)

INT: idêntica ao Piledriver.  
FP: 1 porta removida; MMX realocada; SHUFFLE adicionada no lugar de XBAR (XBAR removida da porta eliminada).

---

## Microarquitetura Excavator (4ª geração)

Novidades em relação ao Steamroller:

- L1-D: **16 → 32 KiB** cada (2 por módulo), **8-way**
- Latência FP: **−1 ciclo** em instruções de ponto flutuante
- Novas instruções: **AVX2, BMI2, RDRAND**; ver [[Instruções SIMD]]
- **PSP** (Platform Security Processor): RNG + **TPM 2.0** → habilita RDRAND
- PCIe **3.0** integrado com 16 pistas (desktop): 2×x4 (=x8) vídeo, UMI 3.0 x4, misc x4
  - Portáteis: apenas 12 pistas externas
- Controlador memória: **DDR3 + DDR4 dual-channel**
  - Stoney Ridge (portátil ultracompacto): DDR4 **single-channel**
- Socket: **AM4** (desktop)
- iGPU **DirectX 12** (GCN3, codinome Volcanic Islands) — exceto Athlon X4
- USB integrado: **4× USB 2.0 + 4× USB 3.2 Gen 1x1**
- SATA-600 integrado: **2 portas**

### Excavator+ (revisão)

Única mudança: decodificador de vídeo **UVD 6.0 → UVD 6.3**. Nenhuma outra diferença significativa.

---

## Soquetes

| Soquete | µarq suportada | Memória | Memória registrada | Canais | PCIe |
|---------|---------------|---------|-------------------|--------|------|
| C32 | Bulldozer, Piledriver | DDR3 | Opcional | 2 | Não |
| G34 | Bulldozer, Piledriver | DDR3 | Opcional | 4 | Não |
| AM3+ | Bulldozer, Piledriver | DDR3 | Não | 2 | Não |
| FM2 | Piledriver | DDR3 | Não | 2 | 2.0 |
| FM2+ | Steamroller, Excavator | DDR3 | Não | 2 | 3.0 (vídeo), 2.0 (E/S) |
| AM4 | Excavator, Excavator+ | DDR4 | Não | 2 | 3.0 |

> [!note] Compatibilidade AM3+ ↔ AM3
> AM3+ tem novas configurações de alimentação. Apenas algumas placas AM3 aceitam processadores AM3+. Verificar lista do fabricante. Processadores AM3+ são mecanicamente compatíveis com AM2/AM2+, mas **não funcionam** nesses soquetes (sem DDR2, alimentação diferente).

> [!note] Compatibilidade FM2 ↔ FM2+
> FM2 encaixa no FM2+. FM2+ **não encaixa** no FM2 (pinos diferentes). FM2 **não compatível** com FM1 (K10.5).

> [!note] AM4
> Formato completamente diferente. Sem compatibilidade com qualquer outro soquete anterior.

---

## Processadores

### FX

Alto desempenho para usuário final; **sem vídeo integrado**. Competia com Core i5 Intel.

**Desktop:**

| Codinome | Ano | Processo | µarq | Núcleos | L3 | Soquete |
|---------|-----|---------|------|---------|----|---------|
| Zambezi | 2011 | 32 nm | Bulldozer | 4/6/8 | 4/8 MiB | AM3+ |
| Vishera | 2012 | 32 nm | Piledriver | 4/6/8 | 4/8 MiB | AM3+ |
| Richland | 2014 | 32 nm | Piledriver | 4 | Não | FM2 |
| Kaveri | 2014 | 28 nm | Steamroller | 4 | Não | FM2+ |

**Portátil (com vídeo integrado):**

| Codinome | Ano | Processo | µarq | Núcleos | L3 | Soquete |
|---------|-----|---------|------|---------|----|---------|
| Kaveri | 2014 | 28 nm | Steamroller | 4 | Não | — |
| Carrizo | 2015 | 28 nm | Excavator | 4 | Não | — |
| Bristol Ridge | 2016 | 28 nm | Excavator+ | 4 | Não | — |

### FirePro

Tentativa malsucedida de APU para workstation. Apenas uma versão lançada:

| Codinome | Ano | Processo | µarq | Núcleos | Soquete |
|---------|-----|---------|------|---------|---------|
| Trinity | 2012 | 32 nm | Piledriver | 4 | FM2 |

### Série A (APU)

Carro-chefe da família Bulldozer. Todos com GPU integrada. AMD usa o termo **APU** (Accelerated Processing Unit) — designação de marketing, sem base acadêmica.

Modelos: A4, A4 PRO, A6, A6 PRO, A8, A8 PRO, A10, A10 PRO, A12, PRO A12 (desktop e portátil); A9 (portátil, Excavator+).

Posicionamento competitivo na época: A4 ↔ Celeron; A6/A8 ↔ Pentium; A10/A12 ↔ Core i3.

**Série A sem cache L3.**

| Codinome | Ano | Processo | µarq | Soquete |
|---------|-----|---------|------|---------|
| Trinity | 2012 | 32 nm | Piledriver | FM2 |
| Richland | 2013 | 32 nm | Piledriver | FM2 |
| Kaveri | 2014 | 28 nm | Steamroller | FM2+ |
| Godavari | 2015 | 28 nm | Steamroller | FM2+ |
| Carrizo | 2016 | 28 nm | Excavator | FM2+/AM4 |
| Bristol Ridge | 2016 | 28 nm | Excavator+ | AM4 |
| Stoney Ridge | 2016 | 28 nm | Excavator+ | — (portátil ultracompacto, DDR4 canal único) |

### Athlon X2 / Athlon X4

Baixo custo; **sem GPU integrada**. X2 = 2 núcleos; X4 = 4 núcleos. Apenas desktop. **Sem modelos Athlon X2 baseados em Excavator.**

| Codinome | Ano | Processo | µarq | Soquete |
|---------|-----|---------|------|---------|
| Trinity | 2012 | 32 nm | Piledriver | FM2 |
| Richland | 2013 | 32 nm | Piledriver | FM2 |
| Kaveri | 2014 | 28 nm | Steamroller | FM2+ |
| Carrizo | 2016 | 28 nm | Excavator | FM2+ |
| Bristol Ridge | 2016 | 28 nm | Excavator+ | AM4 |

### Sempron X2

Abaixo do Athlon X2 em custo/desempenho. Apenas desktop, apenas Piledriver:

| Codinome | Ano | Processo | µarq | Soquete |
|---------|-----|---------|------|---------|
| Trinity | 2012 | 32 nm | Piledriver | FM2 |

### Série E (E2)

3 modelos para portáteis ultracompactos (2016, Excavator+, Stoney Ridge, DDR4 canal único).

### Opteron (Família Bulldozer)

> [!warning] Opteron existe em múltiplas famílias
> Opteron K8 (família F), K10 (família 10h), e agora Bulldozer/Piledriver/Excavator (família 15h). Verificar sempre o CPUID.

**Numeração: 1º dígito indica SMP máximo** (exceto série X3000/Toronto):
- 3xxx: sem SMP, 1 link HT
- 4xxx: 2 processadores, 2 links HT
- 6xxx: até 8 processadores, 3–4 links HT
- X3xxx (Toronto): até 4 processadores

| Codinome | Ano | Processo | µarq | Núcleos | Vias SMP | Soquete |
|---------|-----|---------|------|---------|----------|---------|
| Zurich | 2012 | 32 nm | Bulldozer | 4/8 | 1 | AM3+ |
| Valencia | 2011 | 32 nm | Bulldozer | 4/6/8 | 2 | C32 |
| Interlagos | 2011 | 32 nm | Bulldozer | 4/8/12/16 | 4 | G34 |
| Delhi | 2012 | 32 nm | Piledriver | 4/8 | 1 | AM3+ |
| Seoul | 2012 | 32 nm | Piledriver | 4/6/8 | 2 | C32 |
| Abu Dhabi | 2012 | 32 nm | Piledriver | 4/8/12/16 | 4 | G34 |
| Toronto | 2017 | 28 nm | Excavator | 2/4 | 4 | FP4 (BGA) |

---

## Tabela Comparativa µarqs Bulldozer

| Recurso | Bulldozer | Piledriver | Steamroller | Excavator |
|---------|-----------|-----------|-------------|-----------|
| L1-I por módulo | 64 KiB | 64 KiB | **96 KiB** | 96 KiB |
| L1-D por unidade INT | 16 KiB, 4-way | 16 KiB | 16 KiB | **32 KiB, 8-way** |
| TLB L1-D | 32 entradas | **64 entradas** | 64 entradas | 64 entradas |
| L3 | Sim | Apenas FX/Opteron | **Não** | Não |
| Decodificadores | 1 | 1 | **2** | 2 |
| Fila decoded (loop) | Não | Não | **Sim (2×40)** | Sim |
| FP reg. físicos | 160 | 160 | **176** | 176 |
| DirectPath Double ×2 | Não | **Sim** | Sim | Sim |
| Instruções | FMA4, AVX, AES-NI | +F16C, FMA3, BMI1, TBM | = | +**AVX2, BMI2, RDRAND** |
| iGPU | Não | DX11 (TeraScale 3) | **DX12 (GCN2)** | **DX12 (GCN3)** |
| PCIe | Não | 2.0 | 3.0 (vídeo) | **3.0 total** |
| Memória | DDR3 | DDR3 | DDR3 | **DDR3/DDR4** |
| Turbo Core | 2ª gen | **3ª gen** | 3ª gen | 3ª gen |
| HyperTransport | 3.0 | 3.0/UMI | **Somente UMI** | UMI |
| Socket desktop | AM3+ | FM2 | **FM2+** | **AM4** |
| Store 256-bit alinhado | 3 ciclos | 3 ciclos | **2 ciclos** | 2 ciclos |

---

## Ver também

- [[Processadores AMD Família Zen]] — sucessores: Ryzen, Threadripper, EPYC; retomada AMD
- [[Processadores AMD Família Bobcat]] — família contemporânea para baixo consumo (Bobcat/Jaguar/Puma)
- [[Processadores AMD Família Hammer]] — predecessores: K8, K10, K10.5; soquetes C32/G34 origens
- [[Instruções SIMD]] — FMA4, FMA3, AVX, AVX2, AES-NI, F16C, BMI1/2
- [[Instruções Adicionais x86]] — fusão de instruções, RDRAND, PSP/TPM
- [[Cache]] — L3 compartilhado por módulo; estrutura hierárquica Bulldozer
- [[Desempenho do Processador]] — conceito de módulo vs. núcleo real; PRF; Turbo Core
- [[Microarquitetura Intel Família 6 Parte 3]] — LSD Nehalem análogo à fila decoded do Steamroller
- [[Fabricação de Chips]] — 32 nm (GlobalFoundries) → 28 nm
