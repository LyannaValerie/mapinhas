---
title: Microarquitetura Intel Família 6 (Parte 3)
tags:
  - hardware
  - arquitetura-x86
  - história
  - processadores
  - intel
aliases:
  - Nehalem
  - Westmere
  - Sandy Bridge
  - Ivy Bridge
  - Haswell
  - Broadwell
  - Skylake Intel
  - Sunny Cove
  - Willow Cove
  - Cypress Cove
  - Golden Cove
  - Core i
  - modelo PAO
  - DSB
  - registradores físicos PRF
  - arquitetura em anel
  - QPI
  - DMI
  - UPI
---

# Microarquitetura Intel Família 6 (Parte 3)

Período: **2008–2022**. Cobre Nehalem → Golden Cove. Processadores: Core i (1ª–13ª geração), Xeon Escalonável, Celeron, Pentium.

> [!important] Leia antes
> Este capítulo pressupõe conhecimento de [[Microarquitetura P6 e Processadores Intel Família 6 Parte 1]] e [[Microarquitetura Core e Processadores Intel Família 6 Parte 2]]. Apenas mudanças em relação ao capítulo anterior são detalhadas.

---

## Modelos de Lançamento Intel

### Tick-Tock (2006–2015)

| Fase | O que muda |
|------|-----------|
| **Tock** (nova µarq) | Nova microarquitetura + processo atual |
| **Tick** (atualização) | Mesma µarq, processo menor |

### Processo, Arquitetura, Otimização — PAO (2016–)

Substituiu o tick-tock após dificuldades no processo de 10 nm.

| Tipo | Definição |
|------|-----------|
| Processo | Mesmo núcleo, nó menor |
| Arquitetura | Nova microarquitetura |
| Otimização | Ajustes na µarq atual |

> [!warning] Skylake reciclado
> Intel lançou múltiplas "novas gerações" (7ª–10ª Core i) baseadas em variações da Skylake, sem novidades substanciais. O livro identifica explicitamente todas essas como Skylake.

### Tabela Tick-Tock (2006–2015)

| Core i | Ano | Microarquitetura | Processo | Tipo |
|--------|-----|-----------------|----------|------|
| — | 2006 | Core | 65 nm | Nova |
| — | 2007 | Enhanced Core (Penryn) | 45 nm | Atualização |
| 1ª geração | 2008 | Nehalem | 45 nm | Nova |
| 1ª geração | 2010 | Westmere | 32 nm | Atualização |
| 2ª geração | 2011 | Sandy Bridge | 32 nm | Nova |
| 3ª geração | 2012 | Ivy Bridge | 22 nm | Atualização |
| 4ª geração | 2013 | Haswell | 22 nm | Nova |
| 5ª geração | 2014 | Broadwell | 14 nm | Atualização |
| 6ª geração | 2015 | Skylake | 14 nm | Nova |

### Tabela PAO (2016–2022) — Microarquiteturas Família 6

| Core i | Ano | Produto | Microarquitetura | Processo | Mercado | Tipo |
|--------|-----|---------|-----------------|----------|---------|------|
| 7ª | 2016 | Kaby Lake | Kaby Lake | 14 nm+ | Mainstream/portátil | Otim. Skylake |
| 8ª | 2017 | Kaby Lake Refresh | Kaby Lake Refresh | 14 nm+ | Portátil | Otim. Skylake |
| 8ª | 2017 | Coffee Lake | Coffee Lake | 14 nm++ | Mainstream | Otim. Skylake |
| 8ª/10ª | 2018 | Amber Lake | Amber Lake | 14 nm+ | Portátil baixo consumo | Otim. Skylake |
| 8ª | 2018 | Whiskey Lake | Whiskey Lake | 14 nm++ | Portátil baixo consumo | Otim. Skylake |
| — | 2018 | Cannon Lake | Palm Cove | 10 nm | Portátil | Processo Skylake |
| 9ª | 2018 | Coffee Lake Refresh | Coffee Lake Refresh | 14 nm++ | Mainstream | Otim. Skylake |
| 10ª | 2019 | Comet Lake | Comet Lake | 14 nm+++ | Mainstream | Otim. Skylake |
| 10ª | 2019 | Cascade Lake | Cascade Lake | 14 nm++ | Servidor/entusiasta | Otim. Skylake |
| 10ª | 2019 | Ice Lake | Sunny Cove | 10 nm | Servidor/portátil | Arquitetura |
| — | 2020 | Cooper Lake | Cooper Lake | 14 nm++ | Servidor | Otim. Skylake |
| 11ª | 2020 | Tiger Lake | Willow Cove | 10 nm SF | Portátil | Otim. Sunny Cove |
| 11ª | 2021 | Rocket Lake | Cypress Cove | 14 nm+++ | Mainstream | Processo Sunny Cove |
| 12ª | 2021 | Alder Lake* | Golden Cove | 10 nm ESF / Intel 7 | Mainstream/portátil | Arquitetura |
| — | 2022 | Sapphire Rapids | Golden Cove | Intel 7 | Servidor | Arquitetura |
| 13ª | 2022 | Raptor Lake* | Raptor Cove | Intel 7 | Mainstream/portátil | Otim. Golden Cove |

\* Arquitetura híbrida (P+E cores). Ver [[Microarquitetura Intel Família Atom]].

> [!note] Intel 7 = 10 nm
> "Intel 7" é renomeação do processo 10 nm Enhanced SuperFin (ESF). Comparável em densidade ao 7 nm da concorrência, mas continua sendo um processo de 10 nm.

---

## Nomenclatura Core i

Introduzida a partir do **Nehalem** (2008):

| Nome | Segmento |
|------|---------|
| Core i3 | Entrada |
| Core i5 | Intermediário |
| Core i7 | Topo de linha |
| Core i9 | Entusiasta (HEDT) |

> [!warning] Nomenclatura correta
> "Core i7", não "Intel i7". "Intel i7" não existe.
> "Core i de 12ª geração" ≠ "Intel de 12ª geração". Todos os processadores família 6 são tecnicamente Intel x86 de sexta geração pelo CPUID.

---

## Microarquitetura Nehalem (2008 — 1ª geração Core i)

Novidades em relação ao Enhanced Core (Penryn):

- Pipeline ampliado: **14 → 16 estágios** (Intel não divulga detalhes)
- Segundo BTB (Branch Target Buffer)
- STLB (Second-level TLB) com 512 entradas
- ROB: 96 → **128** entradas; RS: 32 → **36** entradas
- Fusão de instruções disponível em **64 bits** (antes: só 32 bits)
- **LSD movido para IDQ** (ver tópico abaixo)
- MSROM: saída atualizada para **4 µops/ciclo** (antes: 3)
- **Controlador de memória integrado** (ver tópico abaixo)
- **Controlador PCI Express 2.0 integrado** em modelos LGA1156
- Interface externa: **DMI** (LGA1156) ou **QPI** (LGA1366, LGA1567)
- L1: 32 KiB I + 32 KiB D; L2: **256 KiB/núcleo**; L3: compartilhado
- **Hyper-Threading** adicionado à família 6
- **Turbo Boost** (exceto alguns Xeon)
- Estado **C6** (Desligamento Profundo) por núcleo independente
- **SSE4.2** (exceto Pentium e Celeron)
- VT-x 2ª geração
- Soquetes: LGA1156 (2 ch), LGA1366 (3 ch), LGA1567 (4 ch)

### Controlador de Memória Integrado

Antes do Nehalem, controlador de memória ficava no chipset (ponte norte). AMD integrou desde 2003 (Athlon 64).

```
Antes (P6/Core):
  Processador ←[FSB]→ Ponte Norte (controlador memória) ←→ RAM

A partir do Nehalem:
  Processador (com controlador integrado) ←[canal dedicado]→ RAM
  Processador ←[DMI/QPI ponto-a-ponto]→ Chipset ←→ demais dispositivos
```

Ganhos:
1. Comunicação processador↔memória usa clock interno (mais rápido)
2. Canal exclusivo, separado dos outros dispositivos

| Soquete | Canais | Largura total |
|---------|--------|--------------|
| LGA1156 / portáteis | 2 | 128 bits |
| LGA1366 | 3 | 192 bits |
| LGA1567 | 4 | 256 bits |

### Controlador PCI Express 2.0 Integrado

Apenas modelos LGA1156 (2 canais de memória):
- Placa de vídeo x16 direta ao processador; ou
- Duas placas x8 cada (CrossFire/SLI)

Modelos LGA1366/LGA1567: sem PCIe integrado → vídeo via chipset.

### Interface DMI

Processador → chipset, baseada no protocolo PCI Express:

| DMI | Introdução | Taxa/pista | Codificação | BW/direção |
|-----|-----------|-----------|-------------|-----------|
| 1.0 | Nehalem | 2,5 Gbit/s | 10b/8b | 1 GB/s |
| 2.0 | Sandy Bridge | 5 Gbit/s | 10b/8b | 2 GB/s |
| 3.0 | Skylake | 8 Gbit/s | 128b/130b | 4 GB/s |
| 4.0 | Golden Cove | 16 Gbit/s | 128b/130b | 8 GB/s |

4 pistas (normal); alguns portáteis/Atom: 2 pistas; Cypress Cove+: 8 pistas.

### Interface QPI / UPI

Comunicação ponto-a-ponto processador↔chipset (LGA1366/1567) e **entre processadores** em SMP.

20 bits por direção simplex (16 dados + 4 CRC), 2 dados/ciclo → Intel divulga DDR clock.

| Clock | GT/s | Introdução | BW/direção |
|-------|------|-----------|-----------|
| 2,4 GHz | 4,8 | Nehalem | 9,6 GB/s |
| 2,93 GHz | 5,86 | Nehalem | 11,72 GB/s |
| 3,2 GHz | 6,4 | Nehalem | 12,8 GB/s |
| 3,6 GHz | 7,2 | Sandy Bridge | 14,4 GB/s |
| 4,0 GHz | 8,0 | Sandy Bridge | 16 GB/s |
| 4,8 GHz | 9,6 | Haswell | 19,2 GB/s |
| 5,2 GHz | 10,4 | Skylake (UPI) | 20,8 GB/s |
| 8 GHz | 16 | Sapphire Rapids (UPI 2.0) | 32 GB/s |

A partir de Sandy Bridge: todos os processadores têm PCIe integrado → DMI para chipset em todos. QPI/UPI mantido apenas entre processadores SMP.

A partir de Skylake: QPI renomeado para **UPI** (UltraPath Interconnect).

### LSD (Loop Stream Detector) — Evolução

| Microarquitetura | Posição LSD | IDQ | Configuração |
|-----------------|------------|-----|-------------|
| Core / Penryn | IQ (antes do decodificador) | 18 instruções x86 | — |
| **Nehalem** | **IDQ** (µops já decodificadas) | 28 µops | 1×28 |
| Sandy Bridge | IDQ | 28 µops | 2×28 (HT) |
| Ivy Bridge | IDQ | 28/56 µops | 2×28 (HT) ou 1×56 (single-thread) |
| Haswell | IDQ | 56 µops | 1×56 |
| Skylake | IDQ | 64 µops/thread | 2×64 |
| Sunny Cove | IDQ | 70 µops/thread | 2×70 |
| Golden Cove | IDQ | 72 µops/thread | 2×72 ou 1×144 (single-thread) |

> [!warning] Bug Skylake/Kaby Lake HT + LSD
> Bug no HT faz computador apresentar problemas aleatórios. Correção via microcódigo (atualização BIOS) **desabilita o LSD**. Skylake Servidor: LSD desabilitado de fábrica.

Mover LSD para IDQ (Nehalem): processador pode desligar L1-I, busca, pré-decodificação, previsão de desvios e decodificadores durante loops detectados → economia de energia.

### Cache L3 — Arquitetura

L2 de 256 KiB por núcleo + L3 compartilhado entre todos os núcleos:

| Modelo | L3 típico |
|--------|----------|
| Desktop | 8 MiB |
| Portátil | 6–8 MiB |
| Servidor | até 24 MiB |

### Unidades de Execução — Nehalem

6 portas (igual Core). Nova unidade **SIMD MISC** para SSE4.2 (comparação de strings/STTNI).

---

## Microarquitetura Westmere (2010 — 1ª geração Core i)

Tick: Nehalem em **32 nm**. Novidades:

- **GPU integrado** "Intel HD Graphics" (Gen5) — die separado de 45 nm encapsulado junto com die CPU de 32 nm
- Suporte a páginas de **1 GiB**
- **AESNI** (exceto Celeron e Pentium)
- **CLMUL** (Carry-Less Multiplication)

---

## Microarquitetura Sandy Bridge (2011 — 2ª geração Core i)

Tock: nova µarq em **32 nm**. Principais novidades:

- IDQ: 2ª fila adicionada para 2º processador lógico (HT): **2×28**
- **DSB** — Cache de Microinstruções Decodificadas (ver tópico abaixo)
- L2 renomeado para **MLC** (Mid-Level Cache); L3 renomeado para **LLC** (Last-Level Cache)
- LLC agora compartilhado entre núcleos CPU **e** motor gráfico
- BTB dobrado + compressão
- ROB: 128 → **168**; RS: 36 → **54**
- **RRF → PRF** (ver tópico Registradores Físicos abaixo)
- Pipeline: 14–19 estágios (variável, Intel não divulga detalhes)
- **AVX** (exceto Celeron e Pentium — Pentium servidor tem AVX)
- HT removido do Core i5 desktop
- **Turbo Boost 2.0**
- **Arquitetura em anel** (ver tópico abaixo)
- GPU, controlador memória e PCIe agora todos no mesmo die do processador
- GPU opcional: Gen6, DirectX 10.1, Quick Sync (codificação/decodificação vídeo)
- **DMI 2.0** (2 GB/s por direção)
- PCIe 2.0 em LGA1155; PCIe 3.0 em LGA1356 (24 pistas) e LGA2011 (40 pistas)
- QPI substituído por DMI para chipset em todos os modelos; QPI mantido apenas em SMP
- Soquetes: LGA1155 (2 ch), LGA1356 (3 ch), LGA2011 (4 ch)

### DSB — Cache de Microinstruções Decodificadas

Armazena µops já decodificadas (≠ trace cache do Netburst):

| | Trace Cache (Netburst) | DSB (Sandy Bridge+) |
|--|----------------------|---------------------|
| Armazena | µops na ordem de execução — com repetições | µops individuais sem duplicidade |
| Capacidade | 12 kµops | 1.536 µops (Sandy Bridge) |
| Organização | — | 32 conjuntos × 8 vias × 6 µops |

Evolução do DSB:

| Microarquitetura | Capacidade | Saída |
|-----------------|-----------|-------|
| Sandy Bridge | 1.536 µops | 4 µops/ciclo |
| Skylake | 1.536 µops | **6 µops/ciclo** |
| Sunny Cove | **2.304 µops** (48 conjuntos) | — |
| Golden Cove | **4.096 µops** (64×8×8) | **8 µops/ciclo** |

Uso: ~80% do tempo (DSB), ~15% (decodificador), ~5% (MSROM). Quando DSB ativo, L1-I, busca, pré-decodificação, previsão de desvio e decodificadores ficam em sleep.

Hierarquia de fornecimento de µops (prioridade decrescente):
1. **LSD** (IDQ) — loops curtos já decodificados, caminho mais rápido
2. **DSB** — µops decodificadas individuais
3. **Decodificador** — instruções x86 vindo do L1-I
4. **MSROM** — instruções complexas (>4 µops)

### Registradores Físicos (PRF)

Antes (P6 → Penryn): método **RRF** — dados viajam junto com µops pelo ROB.

Sandy Bridge em diante: método **PRF** — µops carregam **endereços** apontando para registradores físicos.

| Aspecto | RRF | PRF |
|---------|-----|-----|
| Dados nas µops | Sim | Não (só ponteiros) |
| Caminhos de dados | Largos (tamanho dado + opcode) | Estreitos (apenas ponteiro) |
| Atualização reg. lógicos | Cópia de dados | Troca de ponteiro na RAT |
| Escalabilidade (AVX-512 etc.) | Difícil — caminhos crescem | Fácil — ponteiro fixo |

RAT = Register Alias Table (tabela de alias de registradores).

Sandy Bridge: 160 registradores físicos inteiros + 144 FP. Aumentados em microarquiteturas posteriores.

### Arquitetura em Anel

Sandy Bridge introduziu anel interno ligando:
- Núcleos de processamento
- Cache L3 (LLC)
- Agente de sistema (controlador memória + PCIe + controle energia + GPU 2D)
- GPU 3D

Quatro anéis físicos: **dados**, **requisição**, **confirmação**, **verificação** (snoop). Clock interno. Protocolo QPI.

Cada componente usa o anel quando disponível; sempre escolhe menor caminho.

### Unidades de Execução — Sandy Bridge

6 portas. **RRF → PRF**. Unidades SIMD renomeadas para **VEC** (Vector). Introdução do AVX (256 bits / YMM).

Novidade nas portas de memória: **2 AGUs** (portas 2 e 3) capazes de carregar ou armazenar (antes: AGU carga separada de AGU armazenamento).

---

## Microarquitetura Ivy Bridge (2012 — 3ª geração Core i)

Tick: Sandy Bridge em **22 nm** (transistores 3D Tri-Gate). Novidades:

- IDQ: 1×56 se single-thread; 2×28 se HT ativo
- PCIe 3.0 em Core i5/i7/Xeon (LGA1155); i3/Celeron/Pentium: PCIe 2.0
- GPU Gen7, DirectX 11, 3 monitores
- **RDRAND** (gerador de números aleatórios digital)
- **SMEP** (Supervisor Mode Execution Protection) — previne EoP via CR4.SMEP
- Instruções REP MOVSB/STOSB mais rápidas
- Suporte DDR3L (portáteis)
- Soquete LGA2011-1 substitui LGA1567 em SMP 8 vias (incompatível)

### SMEP — Proteção contra escalada de privilégio (EoP)

Bloqueia execução de código do espaço de memória do usuário quando processador opera em nível privilegiado (modo supervisor). Habilitar: CR4.SMEP = 1.

---

## Microarquitetura Haswell (2013 — 4ª geração Core i)

Tock: nova µarq em **22 nm**. Novidades:

- IDQ unificado: **1×56**
- ROB: 168 → **192**; RS: 54 → **60**; PRF inteiros: 160 → 168; PRF FP: 144 → 168
- STLB: 512 → **1.024** entradas
- **8 portas de despacho** (+2 em relação ao Core/Sandy Bridge/Ivy Bridge)
- **FMA** e **AVX2** (exceto Celeron e Pentium)
- **BMI1 e BMI2** (manipulação de bits)
- **TSX** (introduzido, depois desabilitado por bug — reintroduzido no Skylake)
- Estados S0ix (economia de energia)
- DDR4 em modelos LGA2011-v3
- PCIe 3.0 com 28 ou 40 pistas em LGA2011-v3
- Soquetes: LGA1150 (2 ch), LGA1356-3 (3 ch), LGA2011-v3 (4 ch)

### Unidades de Execução — Haswell

8 portas. Adições:
- 2ª unidade de desvios (menor capacidade)
- 3ª ALU inteiros
- 2 unidades **BM** (BMI)
- 2 unidades **FMA**
- 2ª unidade **FP MUL** (ambas FP MUL executam FMA)
- AGU adicional para armazenamento (porta 7)
- Unidade **AES** (AESNI)
- Unidades VEC inteiros e FP expandidas para **256 bits** (YMM)

---

## Microarquitetura Broadwell (2014 — 5ª geração Core i)

Tick: Haswell em **14 nm**. Novidades:

- RS: 60 → **64** entradas
- STLB: 1.024 → **1.536** entradas
- **SMAP** (Supervisor Mode Access Prevention) — complementa SMEP; CR4.SMAP = 1
- GPU Gen8, DirectX 11.2
- **RDSEED** (geração mais segura de números pseudoaleatórios)

---

## Microarquitetura Skylake (2015 — 6ª geração Core i)

Última µarq do tick-tock. **14 nm**. Duas versões: Cliente e Servidor.

Novidades:

- 5º decodificador simples → **4 simples + 1 complexo** → 5 µops/ciclo
- DSB: saída ampliada **4 → 6 µops/ciclo**
- IDQ: **2×64 µops**
- ROB: 192 → **224**; RS: 64 → **97**; PRF inteiros: 168 → 180; PRF FP: 168
- DDR4 em todos os modelos; LGA3647 suporta **6 canais**
- L2 Cliente: 256 KiB, 8 vias → 4 vias; L2 Servidor: aumentado para 1 MiB, 16 vias
- Cache L4 em alguns modelos (DRAM, não SRAM)
- GPU Gen9, DirectX 12 (HD Graphics série 500)
- Processador de imagens integrado (até 4 câmeras, 13 MP, 4K@30)
- PCIe 3.0 integrado em todos os modelos
- **DMI 3.0** (4 GB/s/direção)
- **UPI** substitui QPI (servidores SMP)
- TSX reintroduzido (exceto Celeron e Pentium)
- **MPX** (Memory Protection eXtensions) — exceto Celeron e Pentium
- **SGX** — exceto Celeron e Pentium
- **AVX-512** em alguns modelos servidor
- **Speed Shift** substitui SpeedStep
- Soquetes: LGA1151 (2 ch), LGA2066 (4 ch), LGA3647 (6 ch)

> [!warning] LGA1151 incompatibilidade física enganosa
> Duas versões do LGA1151 (Skylake/Kaby Lake vs. Coffee Lake) têm o mesmo nome e permitem encaixar processadores entre si — mas o sistema não funciona.

### Blocos de Entrada — Skylake

```
L1-I (32 KiB)  →  busca/pré-decodificação  →  IQ (18 instr.)
                                                     ↓
                                              decodificador (4S+1C) → 5 µops/ciclo
                                              DSB (1.536 µops)      → 6 µops/ciclo
                                              MSROM                 → 4 µops/ciclo
                                                     ↓
                                              IDQ (2×64 µops) + LSD
                                                     ↓ 4 µops/ciclo
```

### Unidades de Execução — Skylake Cliente (8 portas)

Principais mudanças em relação ao Haswell:
- Funções VEC inteiros fundidas às unidades FP
- 2ª unidade de soma vetorial (ADD) → 2× throughput para soma FP/SIMD
- 3ª ALU vetorial; MOVs absorvidos nas ALUs
- Unidade DIV agora com **pipeline**

### Unidades de Execução — Skylake Servidor (8 portas + AVX-512)

Porta 5 ganha unidades **512 bits** nativas. Portas 0 e 1 operam em conjunto para AVX-512 (256+256 bits).

---

## Microarquitetura Sunny Cove (2019 — 10ª geração Core i / Ice Lake)

Verdadeira sucessora da Skylake. Novidades:

- Paginação de **5 níveis**: endereçamento 48 → **57 bits** (limite/processo: 256 TiB → 128 PiB)
- L1-D: 32 → **48 KiB** (12 vias)
- L2 cliente: 256 KiB → **512 KiB** (8 vias); servidor: **1.280 KiB** (20 vias)
- DSB: 1.536 → **2.304 µops** (48 conjuntos)
- IDQ: 64 → **70 µops/thread** (2×70); saída IDQ: 4 → **5 µops/ciclo**
- ROB: 224 → **352**; PRF FP: 168 → 224
- STLB: 1.536 → **2.048** entradas
- **10 portas de despacho** (+2 vs. Skylake)
- **AVX-512** em todos os processadores (novos subconjuntos)
- **SHANI** (SHA New Instructions)
- **TME** (Total Memory Encryption)
- GPU Gen11, DirectX 12.1

### Unidades de Execução — Sunny Cove (10 portas)

Destaques:
- Unidades AVX-512 nativas em portas 0, 5 (servidor também porta 1 parcial)
- **MUL HI**: 64 bits mais significativos de multiplicação 64×64
- 2ª unidade de armazenamento de dados (2 × 256 bits/ciclo simultâneos)
- 2 portas de carga + 2 portas de armazenamento

---

## Microarquitetura Willow Cove (2020 — 11ª geração Core i / Tiger Lake)

Tick de Sunny Cove em **10 nm SuperFin**. Novidades:

- L2: 512 KiB → **1.250 KiB** (20 vias)
- AVX e AVX2 agora disponíveis nos processadores Celeron e Pentium
- Nova instrução AVX-512: VP2INTERSECT
- **CET** (Control-flow Enforcement Technology) — previne ataques ROP e JOP
- **TDT** (Threat Detection Technology)
- **Key Locker** (proteção de chaves de criptografia)

---

## Microarquitetura Cypress Cove (2021 — 11ª geração Core i / Rocket Lake)

Sunny Cove em **14 nm** (regressão de processo). Novidades:

- PCIe **4.0** com 20 pistas
- DMI 3.0 ampliado para **x8** (dobro de largura de banda)
- GPU Gen12 (Intel Xe), DirectX 12.1

---

## Microarquitetura Golden Cove (2021 — 12ª geração Core i / Alder Lake)

Novidades (comuns Cliente e Servidor):

- Decodificador de comprimento: **16 → 32 bytes/ciclo**
- DSB: → **4.096 µops** (64 conjuntos × 8 vias × 8 µops); saída: **8 µops/ciclo**
- **6 decodificadores simples** (removido o decodificador complexo); saída: **6 µops/ciclo**
- IDQ: **2×72** ou **1×144** (single-thread)
- **12 portas de despacho** (+2 vs. Sunny Cove)
- ROB: 352 → **512**; PRF FP: 224 → 332
- DDR5 e LPDDR5
- PCIe **5.0** (16 pistas) + PCIe 4.0 (4 pistas)
- **DMI 4.0** (8 GB/s/direção em modelos topo, 8 pistas)
- GPU Gen12 (Intel Xe)
- Soquete **LGA1700** (desktop)

Exclusivo versão Servidor:
- L2: → **2 MiB/núcleo**
- AVX-512 subconjunto **FP16**
- **AMX** (Advanced Matrix eXtensions) — aceleração IA/ML
- **DSA** (Data Streaming Accelerator)
- Controlador **8 canais** de memória
- **CXL 1.1** (CXL.io + CXL.cache; não CXL.memory)
- **HBM2E** on-package (modo plano ou cache)
- **4 links UPI 2.0** (16 GT/s)
- Soquete **LGA4677-X**

> [!note] Golden Cove Cliente sem AVX-512
> Alder/Raptor Lake são híbridos (Golden Cove + Gracemont). Núcleos Gracemont não suportam AVX-512 → Intel desabilitou AVX-512 nos modelos cliente para consistência.

### Blocos de Entrada — Golden Cove

```
L1-I (32 KiB)  →  busca/pré-decodificação (32 B/ciclo)  →  IQ
                                                               ↓
                                             6 decodificadores simples → 6 µops/ciclo
                                             DSB (4.096 µops)          → 8 µops/ciclo
                                             MSROM                     → 4 µops/ciclo
                                                               ↓
                                             IDQ (2×72 ou 1×144) + LSD
                                                               ↓ 6 µops/ciclo
```

---

## Evolução das Estruturas Internas (Resumo)

| Microarquitetura | ROB | RS | Portas | PRF Int | PRF FP | IDQ | DSB |
|-----------------|-----|-----|--------|---------|--------|-----|-----|
| P6 | 40 | 20 | 5 | — | — | 6 | — |
| Core | 96 | 32 | 6 | — | — | 18 instr. | — |
| Nehalem | 128 | 36 | 6 | — | — | 28 | — |
| Sandy Bridge | 168 | 54 | 6 | 160 | 144 | 2×28 | 1.536 |
| Haswell | 192 | 60 | 8 | 168 | 168 | 1×56 | 1.536 |
| Broadwell | 192 | 64 | 8 | 168 | 168 | 1×56 | 1.536 |
| Skylake | 224 | 97 | 8 | 180 | 168 | 2×64 | 1.536 |
| Sunny Cove | 352 | — | 10 | — | 224 | 2×70 | 2.304 |
| Golden Cove | 512 | — | 12 | — | 332 | 2×72/1×144 | 4.096 |

---

## Processadores

### Core M

Portáteis ultralow-voltage, TDP ≤ 5 W. Baseados nas mesmas µarqs da linha Core i.

### Celeron e Pentium

- Abaixo do Core i3 em desempenho e recursos
- Alguns recursos da µarq principal ausentes (virtualização, AVX, TSX, SGX…)
- Pentium > Celeron em preço/recursos

### Core i3 / i5 / i7 / i9

Ver tabela de gerações acima. Gerações Sandy Bridge em diante têm numeração explícita. Nota: 1ª geração = Nehalem e Westmere (sem numeração na época de lançamento).

### Xeon

| Série | Mercado | SMP |
|-------|---------|-----|
| Xeon D | SoC servidor baixo consumo | Não |
| Xeon E/E3 | Workstation entrada | Não |
| Xeon W | Workstation/servidor entrada | Não |
| Xeon E5 | Servidor/workstation | Até 4 vias |
| Xeon E7 | Servidor | Até 8 vias |
| Xeon Bronze | Escalonável | 2 vias |
| Xeon Silver | Escalonável | 2 vias |
| Xeon Gold | Escalonável | 4 vias |
| Xeon Platinum | Escalonável | 8 vias |

#### Versões Xeon E3/E5/E7

| Versão | Xeon E3 | Xeon E5 | Xeon E7 |
|--------|---------|---------|---------|
| v1 | Sandy Bridge (LGA1155) | Sandy Bridge (LGA1356/2011) | Westmere (LGA1567) |
| v2 | Ivy Bridge (LGA1155) | Ivy Bridge (LGA1356/2011) | Ivy Bridge (LGA2011-1) |
| v3 | Haswell (LGA1150) | Haswell (LGA1356-3/2011-v3) | Haswell (LGA2011-1) |
| v4 | Broadwell (LGA1150) | Broadwell (LGA2011-v3) | Broadwell (LGA2011-1) |
| v5 | Skylake (LGA1151) | — | — |
| v6 | Kaby Lake (LGA1151) | — | — |
| E | Coffee Lake (LGA1151) | — | — |

#### Gerações Xeon Escalonável (2017+)

| Geração | Microarquitetura | Soquete |
|---------|-----------------|---------|
| 1ª | Skylake Servidor | LGA3647 |
| 2ª | Cascade Lake | LGA3647 |
| 3ª | Cooper Lake ou Sunny Cove | LGA4189 |
| 4ª | Golden Cove | LGA4677-X |

---

## Ver também

- [[Microarquitetura P6 e Processadores Intel Família 6 Parte 1]] — P6 original (1995–2001)
- [[Microarquitetura Core e Processadores Intel Família 6 Parte 2]] — Banias/Core/Penryn (2003–2008)
- [[Microarquitetura Intel Família Atom]] — núcleos E: Bonnell→Gracemont, execução em ordem→OoO, BGA
- [[Processadores Intel Híbridos]] — arquitetura P+E: Lakefield/Alder/Raptor Lake; restrições instruções
- [[Desempenho do Processador]] — LSD, DSB, PRF, ROB, RS, fusão de instruções, Turbo Boost
- [[Cache]] — L3 compartilhado Nehalem; LLC Sandy Bridge; L4 Skylake; HBM2E Golden Cove
- [[Instruções SIMD]] — SSE4.2 (Nehalem), AVX (Sandy Bridge), FMA/AVX2 (Haswell), AVX-512 (Skylake Servidor / Sunny Cove)
- [[Gerenciamento de Consumo Elétrico]] — Turbo Boost, Speed Shift, C6 por núcleo, S0ix
- [[Máquina Virtual]] — VT-x 2ª geração (Nehalem)
- [[Instruções Adicionais x86]] — RDRAND (Ivy Bridge), RDSEED (Broadwell), SGX (Skylake), TSX (Haswell/Skylake), CET (Willow Cove), Key Locker (Willow Cove), AMX (Golden Cove)
- [[Paginação x86]] — paginação 5 níveis 57 bits (Sunny Cove)
- [[Barramento]] — DMI, QPI/UPI, PCIe integrado, CXL
- [[Fabricação de Chips]] — processo 14nm/10nm/Intel 7; tick-tock breakdown; FinFET/Tri-Gate
