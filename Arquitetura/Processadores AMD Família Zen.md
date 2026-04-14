---
title: Processadores AMD — Família Zen
tags:
  - hardware
  - arquitetura-x86
  - história
  - processadores
  - amd
  - ryzen
  - epyc
aliases:
  - Zen
  - Zen 2
  - Zen 3
  - Zen 4
  - Ryzen
  - EPYC
  - Threadripper
  - Infinity Fabric
  - CCX
  - CCD
  - Zeppelin
  - Precision Boost
---

# Processadores AMD — Família Zen

Período: **2017–presente** (até Zen 4, 2022, na publicação do livro).  
Zen foi projetada do zero, retomando os conceitos da família Hammer e abandonando a abordagem de módulos compartilhados da [[Processadores AMD Família Bulldozer|família Bulldozer]].

> [!note] Retomada da AMD
> Três fatores: (1) nova liderança (engenheira como CEO); (2) Zen liderada pelo mesmo arquiteto-chefe das µarqs K7 e K8 (havia saído e foi recontratado); (3) dificuldades da Intel com processo de 10 nm. AMD terceiriza fabricação → acesso a processos mais avançados do que a concorrente. Ver [[Microarquitetura Intel Família 6 Parte 3]].

> [!note] Consoles de videogame
> Xbox Series X/S e PlayStation 5 usam processador baseado em **Zen 2**.

## Microarquiteturas da Família

| Microarquitetura | Ano | Processo (CCD) | Novidade principal |
|-----------------|-----|----------------|-------------------|
| Zen | 2017 | 14 nm | Fundação: CCX, Zeppelin, Precision Boost, SMT |
| Zen+ | 2018 | 12 nm | Precision Boost 2, XFR2, PBO, L2 latency fix |
| Zen 2 | 2019 | 7 nm | Chiplet CCD+IOD, caminhos 256 bits, PCIe 4.0 |
| Zen 3 | 2020 | 7 nm | CCX unificado 8 núcleos, 2 agendadores FP, CET_SS |
| Zen 3+ | 2022 | 6 nm | PCIe 4.0 APU, DDR5, RDNA2, Pluton |
| Zen 4 | 2022 | 5 nm | AVX-512, PCIe 5.0, DDR5 only, AM5, 52-bit físico |

---

## Microarquitetura Zen

### Características Gerais

- L1-I: **64 KiB**, 4-way
- L1-D: **32 KiB**, 8-way
- L2: **512 KiB**, 8-way, por núcleo
- L3: **16-way**, compartilhado por CCX (tamanho varia por modelo)
- **Cache de macroinstruções**: 2.048 entradas (32 sets × 8 vias × 8 macros/linha) → entrega até **8 macros/ciclo**
- Instruções: MMX, SSE–SSE4.2, SSE4a, AVX, AVX2, AES-NI, F16C, CLMUL, FMA3, BMI1, BMI2, ADX, RDRAND, RDSEED (**sem FMA4, sem TBM**); ver [[Instruções SIMD]] e [[Instruções Adicionais x86]]
- **PSP** (Platform Security Processor): RNG + TPM 2.0
- **SME** (Secure Memory Encryption) + **SEV** (Secure Encrypted Virtualization): Ryzen Pro e EPYC apenas; **TSME** (Memory Guard) ativável via BIOS
- **SMEP + SMAP** (proteção contra EoP); Intel introduziu em Ivy Bridge e Broadwell respectivamente
- **SMT**: 2 threads/núcleo (similar ao Hyper-Threading Intel); padrão a partir do Ryzen 5 (Ryzen 3 geralmente sem SMT, com exceções)
- Controlador de memória: **DDR4** — 2ch (AM4/portátil), 4ch (TR4), 8ch (SP3)
- **PCIe 3.0** integrado
- iGPU **DX12.1** (GCN5/Vega, **VCN 1.0**): apenas modelos G/GE e portáteis
- SoC (sem chipset) em portáteis e servidores
- Áudio integrado (necessita apenas de codec externo)
- Novos soquetes: **AM4** (desktop), **TR4** (HEDT), **SP3** (servidor) — incompatíveis com anteriores

### Pipeline

- Busca: **32 bytes** do L1-I → **IBQ** (20 entradas × 16 bytes; **10/thread** com SMT)
- Seleção: até **4 instruções x86/ciclo** para decodificação
- **4 decodificadores**; tipos: DirectPath Single, DirectPath Double, VectorPath (igual K8)
- Bits de pré-decodificação **não gravados de volta** no L1-I (igual Bobcat/Jaguar, diferente de Hammer)
- **Fusão**: compare + branch condicional → 1 macroinstrução
- **Cache de macroinstruções** (Op Cache): ativado quando previsão de desvios detecta desvio; retorna para decodificadores quando instrução ausente. Entrega **8 macros/ciclo** (2× os decodificadores). Aumenta desempenho E reduz consumo ao evitar busca+decodificação
- Fila de macroinstruções: **dividida em 2 com SMT ativo**
- **MSROM** (microcódigo): localizado após fila de macroinstruções; envia direto para RCU
- **SEM** (Stack Engine Memfile): executa instruções de pilha sem usar AGU; ver [[Arquitetura/Pilha]]
- Despacho: até **6 macros/ciclo** (FP: máx **4 macros/ciclo** nas µarqs até Zen 2)
- **RCU** (Retire Control Unit): rastreia execução, controla renomeamento e exceções; contém ROB
- **ROB**: **192 entradas** (96/thread) — AMD chama de "fila de retirada"
- Arquiteturas separadas INT e FP (diferente da Intel, que mistura em único agendador)
- **PRF INT**: 168 entradas (64-bit); **PRF FP**: 160 entradas (128-bit)
- Agendadores INT: **6** total — 4 para inteiros + 2 para memória, **14 entradas** cada
- Agendador FP: **1 unificado**, 36 entradas + **NSQ** 64 entradas (fila de espera pré-agendador, para FP com alta latência)
- FP units: **128 bits** → AVX 256-bit = **2 macroinstruções**
- Load/store: **2 cargas de 128 bits + 1 armazenamento de 128 bits** por ciclo
- Retirada: até **8 macros/ciclo**
- Estágios de pipeline: **não divulgados pela AMD**

### Unidades de Execução (Zen)

**Inteiros** (4 portas de despacho, itens adicionais — posição de MUL/DIV/BRU/CRC é suposição do autor):

| Unidade | Função |
|---------|--------|
| ALU 0–3 | Aritmética/lógica |
| AGU 0–1 | Geração de endereços |
| MUL | Multiplicação inteira (pipelined) |
| DIV | Divisão inteira |
| BRU × 2 | Branch resolution |
| CRC | Cálculo de CRC |

**Ponto flutuante** (4 portas de despacho, 128 bits):

| Unidade | Função |
|---------|--------|
| FMUL × 2 | Multiplicação FP, FMA, comparação, blend |
| FADD × 2 | Soma e subtração FP |
| FCVT | Conversão FP (exceto pack/unpack) |
| FDIV | Divisão e raiz quadrada FP; executa 2 µops simultaneamente |
| FMISC × 4 | Movimentação de dados, lógica |
| FSTORE | Carga para GPR + armazenamento em memória |
| VADD × 2 | Vetorial inteira: adição, subtração, comparação |
| VMUL | Vetorial inteira: multiplicação |
| VSHUF × 2 | Vetorial inteira: shuffle, pack, unpack, permutação (shuffle complexo: apenas porta 1) |
| VSHIFT | Vetorial inteira: rotação de bits esq/dir |
| AES × 2 | AES-NI |
| CLMUL | CLMUL |
| VMISC × 4 | Vetorial misc |

---

## Estrutura Modular

### CCX e Zeppelin (Zen / Zen+)

```
CCX = 1–4 núcleos + cache L3 compartilhado
Chip = 1 ou 2 CCX
Zeppelin = chip com 2 CCX + controladores I/O (memória, PCIe, USB)
```

| Processador | Chips (Zeppelins) | CCX/chip | Núcleos máx | Canais DDR4 |
|-------------|------------------|----------|-------------|-------------|
| Desktop/laptop | 1 | 1 ou 2 | 8 | 2 |
| Ryzen Threadripper | 2 | 2 | 16 | 4 |
| EPYC | 4 | 2 | 32 | 8 |

> [!note] Threadripper: 4 chips físicos, 2 falsos
> Threadripper usa mesmo encapsulamento do EPYC. 2 chips são dummy (sustentação mecânica para o IHS). Apenas 2 chips são funcionais.

**Infinity Fabric**: interconexão entre CCX e entre Zeppelins. Opera no **clock de memória** (não no clock interno do processador).

**iGPU**: implementada em módulo separado dentro do die.

### Chiplet: CCD + IOD (Zen 2 em diante)

Na Zen 2, o Zeppelin foi dividido em dois tipos de chip:

| Chip | Nome | Conteúdo | Processo |
|------|------|---------|---------|
| CCD | Core Complex Die | Núcleos de processamento (1 ou 2 CCX) | **7 nm** |
| IOD | Input/Output Die | Controlador de memória, PCIe, USB | 12 nm (consumer) / 14 nm (servidor) |

> [!note] Chiplet — fabricar núcleos e I/O separados
> Núcleos no processo mais avançado (7 nm → 5 nm). I/O em processo mais barato (12/14 nm). Maior yield e menor custo. Ver [[Fabricação de Chips]].

**Zen 2 CCX**: mantém 1-2 CCX com até 4 núcleos cada.  
**Zen 3 CCX**: **1 CCX por CCD** com até **8 núcleos** compartilhando o **mesmo L3** → acesso direto entre todos os núcleos do CCD ao L3 (antes, núcleos de CCX diferente cruzavam via Infinity Fabric).

**Zen 3 APU** (com iGPU): projeto **monolítico** — 1 pastilha única em 7 nm (sem chiplet).

---

## Controladores de Memória

| Processador | Modo | Canais | Configuração |
|-------------|------|--------|--------------|
| Desktop/laptop (1 Zeppelin) | — | 2 (DDR4) | — |
| Threadripper (2 Zeppelins) | UMA ou NUMA | 4 (DDR4) | 2 controladores independentes |
| EPYC (4 Zeppelins) | UMA ou NUMA | 8 (DDR4) | 4 controladores independentes |

- **UMA** (Uniform / distribuído): controladores trabalham em conjunto como se fossem um único circuito
- **NUMA** (Non-Uniform / local): cada controlador atende apenas ao Zeppelin/CCD onde está

---

## Controladores PCI Express

### Zen / Zen+

Cada Zeppelin: **32 pistas PCIe 3.0**.

| Processador | GPU | NVMe/SATA | GPP | Total |
|-------------|-----|-----------|-----|-------|
| Desktop sem iGPU | 16 | 4 | 4 | **24** |
| Desktop / portátil com iGPU | 8 | 4 | 4 | **16** |
| Ryzen Threadripper (TR4) | 48 | 12 | 4 | **64** |
| EPYC (SP3) | — | — | — | **128** (configuração livre) |

Configurações do grupo NVMe/SATA (4 pistas):
- `x4` → 1 SSD NVMe
- `x2 SSD + 2×x1 SATA` → 1 SSD + 2 portas SATA-600
- `x2 SATA Express + x2 GPP` (raramente usado)

GPP desktop = conexão com chipset. GPP portátil/servidor = livre (rede, Wi-Fi, USB, etc.).

### Zen 2

PCIe **4.0** nos modelos sem iGPU; PCIe **3.0** mantido nos modelos com iGPU.

| Processador | Versão | GPU | NVMe/SATA | GPP | Total |
|-------------|--------|-----|-----------|-----|-------|
| Desktop sem iGPU | 4.0 | 16 | 4 | 4 | **24** |
| Desktop / portátil com iGPU | 3.0 | 8 | 4+4* | 4 | **20** |
| Ryzen Threadripper (sTRX4) | 4.0 | 48 | 8 | 8† | **64** |
| Ryzen Threadripper Pro (sWRX8) | 4.0 | — | — | — | **128** |
| EPYC | 4.0 | — | — | — | **128** (≤32 pistas como SATA) |

*APU ganhou 2º controlador NVMe/SATA (2 grupos de 4 pistas); 2º grupo pode ser GPP.  
†Threadripper: 8 pistas para chipset (antes 4×PCIe3.0); removeu 1 NVMe → 2 controladores (antes 3).

EPYC SMP 2-vias: **47 pistas** entre processadores (antes 64) → até **162 pistas disponíveis** (antes 128).

### Zen 3+

APU: controladores PCIe atualizados para **4.0** (antes 3.0 para APU).

### Zen 4

PCIe **5.0**, **28 pistas**.

| Grupo | Pistas | Uso |
|-------|--------|-----|
| GPU | 16 (×16 ou 2×8) | Placa de vídeo |
| GPP | 4 | Chipset (obrigatório) |
| GPP | 4 | Slot M.2 (obrigatório) |
| GPP | 4 | Livre (NVMe, USB4, Thunderbolt 4, etc.) |

EPYC Zen 4: soquete **SP5** (12 canais DDR5) ou **SP6** (6 canais DDR5).

---

## Controladores USB

### Zen

| Processador | USB 3.2 G2x1 (3.1) | USB 3.2 G1x1 (3.0) | USB 2.0 |
|-------------|---------------------|---------------------|---------|
| Desktop sem iGPU | 0 | 4 | 0 |
| Desktop / portátil iGPU | 2 | 0 | 4 |
| Ryzen Threadripper | 0 | 8 | 0 |
| EPYC | 0 | 4 | 4 |

Modelos com iGPU: 1 controlador DisplayPort funciona também como USB → até +2 portas USB 3.2 G2x1 (combináveis em conector tipo C), a critério do fabricante.

### Zen 2

Upgrade para USB 3.2 G2x1 em desktop/portátil e Threadripper. EPYC: sem alteração.

| Processador | USB 3.2 G2x1 | USB 3.2 G1x1 | USB 2.0 |
|-------------|-------------|-------------|---------|
| Desktop sem iGPU | 4 | 0 | 0 |
| Desktop / portátil iGPU | 2 | 0 | 4 |
| Ryzen Threadripper | 4 | 0 | 0 |
| Ryzen Threadripper Pro | 4 | 0 | 0 |
| EPYC | 0 | 4 | 4 |

### Zen 4

**4× USB 3.2 Gen 2 (3.1) + 1× USB 2.0**. Três portas suportam DisplayPort 2.0.

---

## Tecnologias de Clock

### Hierarquia

```
Cool'n'Quiet / PowerNow!  ← estados de economia (clock mínimo)
Clock Base                ← operação normal
Clock Boost 1             ← ≤2 núcleos ativos (Precision Boost 1 apenas)
Clock Boost 2             ← >2 núcleos ativos (Precision Boost 1 apenas)
XFR / XFR2               ← extensão automática do boost (+50–200 MHz)
PBO                       ← overclock manual configurável (anula garantia)
```

### Precision Boost (Zen)

- Substitui Turbo Core
- Ajuste em **passos de 25 MHz** (antes: 100 MHz)
- **1.000+ leituras de sensores/segundo** (centenas de sensores no die)
- Limites avaliados: clock boost, temperatura máxima, potência máxima, corrente máxima do VRM
- **2 clocks boost**: maior (≤2 núcleos ativos); menor (>2 núcleos ativos)

### XFR — eXtended Frequency Range (Zen)

- Amplia os clocks boost **acima dos valores rotulados** se houver margem térmica/elétrica e refrigeração adequada
- Amplitude: **+50 a +200 MHz** em incrementos de 25 MHz

### Precision Boost 2 + XFR2 (Zen+)

- **Único clock boost** independente do número de núcleos ativos
- XFR2: amplia esse único clock boost

### PBO — Precision Boost Overdrive (Zen+; todos Ryzen a partir Zen 2)

- Permite configuração manual dos 3 parâmetros de limite do Precision Boost:
  - **PPT** (Platform Power Threshold): potência máxima em W
  - **TDC** (Thermal Design Current): corrente sustentada máxima do VRM em A
  - **EDC** (Electrical Design Current): corrente de pico máxima do VRM em A
- Overclock automático pelo próprio processador ao relaxar limites
- **Anula garantia**; deve ser ativado manualmente na configuração da placa-mãe

### PBO2 (Zen 3)

- Ajuste fino de **tensão mais baixa** → menor dissipação → maior margem de clock
- Também aumenta desempenho em aplicações **single-thread**

---

## Microarquitetura Zen 2 — Estruturas Internas

| Estrutura | Zen | Zen 2 |
|-----------|-----|-------|
| L1-I | 64 KiB | **32 KiB** |
| Cache de macros | 2.048 | **4.096** (64 sets × 8 vias) |
| IBQ | 20 entradas | 20 entradas |
| Agendadores INT | 4×14 entradas | 4×**16** entradas |
| Agendadores MEM | 2×14 entradas | **1×28** entradas (2 macros/ciclo) |
| PRF INT | 168 | **180** |
| ROB | 192 (96/t) | **224** (112/t) |
| AGUs | 2 | **3** (3ª apenas store) |
| Largura interna FP | 128 bits | **256 bits** |
| AVX 256-bit | 2 macros | **1 macro** |
| Cargas/ciclo | 2×128b | **2×256b** |
| Armazenamento/ciclo | 1×128b | **1×256b** |

Configuração de unidades de execução: igual Zen (Figura 32.3), exceto adição da 3ª AGU.

---

## Microarquitetura Zen 3 — Estruturas Internas

| Estrutura | Zen 2 | Zen 3 |
|-----------|-------|-------|
| CCX por CCD | 2 (4 núcleos cada) | **1 (8 núcleos)** |
| IBQ | 20 entradas | **24** (12/thread) |
| Agendadores INT | 4×16 | **4×24** (2 macros/ciclo cada) |
| Agendador MEM | 1×28 | **Removido** (+ unidade de dependência de memória) |
| Agendadores FP | 1×36 | **2×32** |
| Portas despacho FP | 4 | **6** (FP aceita 6 macros/ciclo) |
| PRF INT | 180 | **192** |
| ROB | 224 (112/t) | **256** (128/t) |
| Cargas/ciclo | 2×256b | **3×256b** |
| Store/ciclo | 1×256b | 1×128b ou 256b OU 2×64b |
| CET_SS | Não | **Sim** (proteção ROP/JOP) |

Latências reduzidas: FMA 5→**4** ciclos; INT DIV (8/16/32/64-bit) 17/22/30/46→**10/12/14/20** ciclos.

### Unidades de Execução (Zen 3)

**INT (8 portas de despacho)**:

| Unidade | Novidade vs. Zen |
|---------|-----------------|
| ALU 0–3 | Mesmas |
| AGU 0–1 (carga) | Mesmas |
| AGU 2 (store only) | Mesma (da Zen 2) |
| MOV × 3 | **Novas** (movimentação de dados) |
| BRU × 2 | 2ª BRU agora em **porta separada** |
| MUL (+ CRC incorporado) | CRC fusionado com MUL |
| DIV | Mesma |

**FP (6 portas de despacho)** — portas pares → agendador 1; portas ímpares → agendador 2:

| Adição vs. Zen | Detalhes |
|---------------|---------|
| FPMUL × 2 | Igual |
| FPADD × 2 | Igual |
| **FSTORE × 2** | 2ª unidade de armazenamento adicionada |
| **F2I × 2** | Conversão FP→inteiro (mencionado em alto nível AMD) |
| **VADD × 4** | Antes: ×2 |
| **VMUL × 2** | Antes: ×1 |
| **VSHIFT × 2** | Antes: ×1 |
| **FCVT × 2** | Antes: ×1 |
| **CLMUL × 2** | Antes: ×1 |
| VSHUF × 2 | Igual |
| AES × 2 | Igual |
| FMISC × 4 | Igual |
| VMISC × 4 | Igual |
| FDIV | Igual |

---

## Zen 3+ — Novidades

- PCIe **4.0** também nos modelos com iGPU
- Memória: **DDR5** e **LPDDR5** (além de DDR4); ver [[Cache]]
- iGPU: **RDNA2** (DX12.2), ray tracing, **VCN 3.1**, 4 saídas independentes
- **USB4** + porta tipo C com DisplayPort
- **Pluton** (Microsoft): processador de segurança integrado
- Novos modos de economia de energia (maior porção do chip desligada em idle/vídeo)
- Cancelamento de ruído por **hardware** no circuito de áudio

---

## Zen 4 — Novidades Principais

- L2: **512 KiB → 1 MiB** por núcleo
- Cache de macros: **4.096 → 6.144** entradas
- **AVX-512 + GFNI** — caminhos internos mantidos em 256 bits (AVX-512 = 2 macros de 256b)
- Endereçamento físico: **48 → 52 bits**; linear: **48 → 57 bits** (paginação 5 níveis); ver [[Paginação x86]]
- PCIe **5.0**, 28 pistas (nova configuração)
- Memória: **apenas DDR5 / LPDDR5** (sem DDR4)
- EPYC: até **12 canais** (SP5) ou 6 canais (SP6)
- Soquetes: **AM5** (desktop LGA), **SP5**, **SP6**
- iGPU em **todos** os modelos (antes: apenas G/GE e portáteis)
- USB: 4× USB 3.2 Gen 2 + 1× USB 2.0; 3 portas com DisplayPort 2.0

> [!note] AM5 = LGA (pinos no soquete)
> AM4 e anteriores AMD usavam PGA (pinos no processador). AM5 muda para LGA como Intel. Ver [[Fabricação de Chips]].

---

## Tabela Comparativa de Estruturas Internas

| Estrutura | Zen | Zen 2 | Zen 3 | Zen 4 |
|-----------|-----|-------|-------|-------|
| L1-I | 64 KiB | **32 KiB** | 32 KiB | 32 KiB |
| L1-D | 32 KiB | 32 KiB | 32 KiB | 32 KiB |
| L2 | 512 KiB | 512 KiB | 512 KiB | **1 MiB** |
| Cache macros | 2.048 | 4.096 | 4.096 | **6.144** |
| IBQ | 20 (10/t) | 20 | **24** (12/t) | 24 |
| Ag. INT | 6×14 | 4×16+1×28 | **4×24** | 4×24 |
| Ag. FP | 1×36 | 1×36 | **2×32** | 2×32 |
| Portas FP | 4 | 4 | **6** | 6 |
| PRF INT | 168 | 180 | **192** | 192 |
| PRF FP | 160 | 160 | 160 | 160 |
| ROB | 192 (96/t) | 224 | **256** (128/t) | 256 |
| AGUs | 2 | **3** | 3 | 3 |
| Largura FP | 128b | **256b** | 256b | 256b |
| Instruções | AVX2 | AVX2 | AVX2 | **AVX-512** |
| PCIe | 3.0 | **4.0** | 4.0 | **5.0** |
| Memória | DDR4 | DDR4 | DDR4/DDR5 | **DDR5 only** |
| iGPU motor | GCN5/Vega | GCN5+VCN2 | RDNA2(Zen3+) | **RDNA2 all** |
| Chiplet | Não | **CCD+IOD** | CCD+IOD | CCD+IOD |
| Processo | 14 nm | **7 nm** | 7 nm | **5 nm** |

---

## Processadores

> [!warning] Numeração Ryzen é enganosa
> O primeiro dígito do modelo Ryzen **não indica a microarquitetura**. Há descompasso entre séries desktop e portátil. Sempre verificar o codinome para identificar a µarq correta.

### Ryzen (mainstream)

Desktop (soquete **AM4**, exceto Zen 4 = **AM5**):

| Codinome | Série | Ano | Processo | µarq | iGPU | PCIe |
|---------|-------|-----|---------|------|------|------|
| Summit Ridge | 1000 | 2017 | 14 nm | Zen | Não | 3.0 |
| Raven Ridge | 2000G/GE | 2018 | 14 nm | Zen | Sim | 3.0 |
| Pinnacle Ridge | 1000AF / 2000 | 2018 | 12 nm | Zen+ | Não | 3.0 |
| Picasso | 3000G/GE | 2019 | 12 nm | Zen+ | Sim | 3.0 |
| Matisse | 3000 | 2019 | 7 nm | Zen 2 | Não | **4.0** |
| Renoir | 4000G/GE | 2020 | 7 nm | Zen 2 | Sim | 3.0 |
| Vermeer | 5000 | 2020 | 7 nm | Zen 3 | Não | 4.0 |
| Cezanne | 5000G/GE | 2021 | 7 nm | Zen 3 | Sim | 3.0 |
| Raphael | 7000 | 2022 | **5 nm** | Zen 4 | **Sim** | **5.0** |

Portátil (PCIe 3.0 exceto Rembrandt = PCIe 4.0):

| Codinome | Série | Ano | Processo | µarq |
|---------|-------|-----|---------|------|
| Raven Ridge | 2000 | 2017 | 14 nm | Zen |
| Dali | 3200U/3250U | 2019 | 14 nm | Zen |
| Picasso | 3000 | 2019 | 12 nm | Zen+ |
| Renoir | 4000 | 2020 | 7 nm | Zen 2 |
| Lucienne | 5300/5500/5700 | 2021 | 7 nm | Zen 2 |
| Cezanne | 5400/5600/5800/5900 | 2021 | 7 nm | Zen 3 |
| Barceló | 5x25C | 2022 | 7 nm | Zen 3 |
| Rembrandt | 6000 | 2022 | **6 nm** | Zen 3+ |

### Ryzen Threadripper (HEDT / workstation)

| Codinome | Série | Ano | Processo | µarq | Soquete |
|---------|-------|-----|---------|------|---------|
| Whitehaven | 1900 | 2017 | 14 nm | Zen | TR4 |
| Colfax | 2900 | 2018 | 12 nm | Zen+ | TR4 |
| Castle Peak | 3900 | 2019 | 7 nm | Zen 2 | sTRX4 / sWRX8 |
| Genesis | 5000 | 2021 | 7 nm | Zen 3 | sTRX4 / sWRX8 |

Pro (workstation) = sWRX8; HEDT = sTRX4.

### Athlon (baixo custo)

Desktop (AM4):

| Codinome | Série | Ano | Processo | µarq | SMT | Precision Boost |
|---------|-------|-----|---------|------|-----|----------------|
| Raven Ridge | Athlon/Pro 200 | 2018 | 14 nm | Zen | Sim | **Não** |
| Picasso | Athlon Pro 300 | 2019 | 12 nm | Zen+ | Sim | **Não** |
| Picasso | Athlon Gold 3000 | 2019 | 12 nm | Zen+ | **Não** | Sim |

Portátil:

| Codinome | Série | Ano | µarq |
|---------|-------|-----|------|
| Raven Ridge | 200 | 2019 | Zen |
| Dali | Pro/Silver/Gold 3000 | 2020 | Zen 2 |

### AMD 3000 (portátil baixo consumo)

Mesma pastilha Dali do Athlon. Precision Boost presente. 1 ou 2 canais de memória dependendo do modelo. SMT varia por modelo.

### EPYC (servidor — substitui Opteron)

| Geração | Codinome | Ano | Processo | µarq | Núcleos máx | Soquete |
|---------|---------|-----|---------|------|-------------|---------|
| 1ª | Naples | 2017 | 14 nm | Zen | 32 | SP3 |
| 2ª | Rome | 2019 | 7 nm | Zen 2 | 64 | SP3 |
| 3ª | Milan | 2021 | 7 nm | Zen 3 | 64 | SP3 |

- SMP máximo: **2 vias** (antes Opteron suportava até 8)
- Com SMT: EPYC 64 núcleos = **128 threads**; sistema 2-via = **128 núcleos / 256 threads**
- Modelos com sufixo "**P**": sem suporte a SMP
- Vantagem vs. Opteron 8-vias: menor custo, menor espaço, menor consumo, menor geração de calor

---

## Ver também

- [[Processadores AMD Família Bulldozer]] — predecessores; contexto da crise AMD
- [[Processadores AMD Família Bobcat]] — família contemporânea baixo consumo (descontinuada 2015)
- [[Microarquitetura Intel Família 6 Parte 3]] — concorrente Intel; dificuldade com processo 10 nm
- [[Instruções SIMD]] — AVX, AVX2, AVX-512, AES-NI, FMA3, BMI, F16C
- [[Instruções Adicionais x86]] — ADX, RDRAND, RDSEED, PSP/TPM, SMEP/SMAP, CET_SS
- [[Paginação x86]] — Zen 4: endereçamento físico 52b e linear 57b (paginação 5 níveis)
- [[Cache]] — L3 CCX; NSQ pre-queue FP; chiplet CCD 7nm vs IOD 12nm
- [[Desempenho do Processador]] — Precision Boost, XFR, PBO, SMT, SEM, Op Cache
- [[Fabricação de Chips]] — chiplet; 14→12→7→6→5nm; AM5 LGA vs AM4 PGA
- [[Arquitetura/Pilha]] — SEM executa instruções de pilha sem usar AGU
- [[Concorrência e Paralelismo]] — SMT, NUMA/UMA, estrutura modular
