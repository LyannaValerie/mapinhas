---
title: Microarquitetura Intel — Família Atom
tags:
  - hardware
  - arquitetura-x86
  - história
  - processadores
  - intel
aliases:
  - família Atom
  - Bonnell
  - Saltwell
  - Silvermont
  - Airmont
  - Goldmont
  - Goldmont Plus
  - Tremont
  - Gracemont
  - SoFIA
  - núcleos E
  - núcleos eficientes
---

# Microarquitetura Intel — Família Atom

Período: **2008–presente**. Foco: baixo consumo (TDP ≤ 10 W), dispositivos móveis, embarcados, híbridos.

> [!important] Família 6 pelo CPUID
> Processadores Atom se identificam como família 6 pelo CPUID — diferenciados pelos campos Modelo e Modelo Estendido, não pela família.

---

## Contexto

- Foco: desempenho/watt (eficiência), não desempenho absoluto
- Nomes comerciais: **Atom**, **Celeron**, **Pentium Silver** — e **Core i** nos processadores híbridos
- Todos em encapsulamento **BGA** (soldados na placa — sem soquete)
- A partir de 2020: núcleos Atom usados como núcleos "E" (Eficiente) em processadores híbridos P+E

> [!note] Híbridos (Alder/Raptor Lake)
> Processadores híbridos com núcleos "P" (Golden/Raptor Cove) + núcleos "E" (Gracemont) são explicados no capítulo 27. Este capítulo cobre apenas os núcleos E.

---

## Evolução das Microarquiteturas

| Ano | Microarquitetura | Processo | Tipo | Execução |
|-----|-----------------|----------|------|---------|
| 2008 | **Bonnell** | 45 nm | Nova | Em ordem |
| 2010 | **Saltwell** | 32 nm | Atualização | Em ordem |
| 2013 | **Silvermont** | 22 nm | Nova | Fora de ordem |
| 2015 | **Airmont** | 14 nm | Atualização | Fora de ordem |
| 2016 | **Goldmont** | 14 nm | Nova | Fora de ordem |
| 2017 | **Goldmont Plus** | 14 nm | Otimização | Fora de ordem |
| 2020 | **Tremont** | 10 nm | Nova | Fora de ordem |
| 2021 | **Gracemont** | 10 nm | Nova | Fora de ordem |

Exceção: **SoFIA** (2015–2016) — Silvermont em **28 nm** para smartphones, com modem 3G embutido. Modelos 4G LTE anunciados mas nunca lançados (Intel abandonou mercado de smartphones em 2016).

Dois grupos de µarqs:
- **Bonnell/Saltwell**: execução **em ordem** + Hyper-Threading
- **Silvermont em diante**: execução **fora de ordem** sem Hyper-Threading

---

## Microarquitetura Bonnell (2008) / Saltwell (2010)

Execução **em ordem**, **superescalar 2 vias**, **Hyper-Threading**.

Sem OoO → sem execução especulativa, sem renomeamento de registradores.

### Características

| Atributo | Valor |
|----------|-------|
| L1-I | 32 KiB (8 vias) — fisicamente 36 KiB (4 KiB para marcadores de comprimento) |
| L1-D | 24 KiB (6 vias) |
| L2 | 512 KiB (8 vias) por núcleo |
| Hyper-Threading | Sim (maioria dos modelos) |
| SSE | SSE3, SSSE3 |
| 64 bits | Sim (exceto alguns modelos) |
| SpeedStep | Sim (exceto alguns modelos) |
| Virtualização | Sim (exceto alguns modelos) |
| GPU (Bonnell) | Gen3, DirectX 9.0 (exceto primeiros modelos Diamondville) |
| GPU (Saltwell) | PowerVR, DirectX 9.0c |

### Pipeline Bonnell — 16 estágios (inteiros) / 19 (FP)

| Grupo | Estágios | Detalhe |
|-------|---------|---------|
| Busca | 3 | Leitura ponteiro instrução + cache L1-I |
| Decodificação | 3 | Pré-decodificação + marcadores → cache L1-I; 2 decodificadores complexos (1 µop/instr. em 96% das vezes) |
| Envio | 3 | SC (troca contexto) → IS (agendamento) → IRF (leitura operando) |
| Acesso cache dados | 3 | AG + DC + DC — **obrigatório mesmo sem acesso memória** (corrigido no Silvermont) |
| Execução | 1+ | 1 ciclo (inteiros); mais ciclos (FP) |
| Finalização | 3 | FT × 2 (tratamento exceções + HT) + IWB/DC1 (write-back + atualização L1-D) |

### Marcadores de comprimento no L1-I (Bonnell)

Cache L1-I fisicamente = 36 KiB (32 KiB dados + 4 KiB marcadores de comprimento).

- ILD (Instruction Length Decoder) identifica limites de instruções x86
- Marcadores replicados no L1-I → instrução re-buscada não precisa re-identificar comprimento → economia de **2 ciclos**

### Fila de Instruções (Bonnell)

- 2 filas × 16 µops cada
- Se 2ª thread ociosa: filas combinadas em 1×32 (configuração **2×16**)

### Unidades de Execução — Bonnell (2 portas)

| Porta | Unidades |
|-------|---------|
| 0 | ALU 0, SIMD ALU, SHIFT, LEA, CARGA/ARMAZTO, DESVIOS, DIV (também int MUL/DIV) |
| 1 | ALU 1, SIMD ALU, SHUFFLE, FP ADD, SIMD MUL, FP MUL/CARGA, FP MOV, FP ROM, FP ARMAZTO |

Particulares:
- SIMD ALU/SHUFFLE: dados 128 bits; int 64 bits só na porta 0
- FP ADD: 128 bits para ADDPS/SUBPS; outras ops FP: 64 bits
- FP ADD com referência de memória: despachada em **ambas as portas** simultaneamente

Limite: instrução em paralelo só se não depender de dados da anterior (**em ordem**).

Solução para FP + INT em paralelo: **Reconhecimento Seguro de Instrução** — identifica instruções FP que podem gerar exceções, permite finalização imediata de instruções INT quando possível.

### Alocação por ciclo — Evolução

| Microarquitetura | µops alocadas/ciclo |
|-----------------|-------------------|
| Bonnell / Silvermont | 2 |
| Goldmont | 3 |
| Goldmont Plus / Tremont | 4 |
| Gracemont | 5 |

---

## Microarquitetura Silvermont (2013) / Airmont (2015)

Adição de **execução fora de ordem** (OoO), execução especulativa e renomeamento de registradores (método **PRF**).

HT removido → estrutura **modular**: módulo = 2 núcleos + cache L2 compartilhado. Processador de 4 núcleos = 2 módulos.

### Pipeline Silvermont

Estágios de acesso ao cache de dados: **só presentes se instrução acessa memória** (corrigindo Bonnell).

| Grupo | Estágios |
|-------|---------|
| Busca | 3 |
| Decodificação | 3 (2 decodificadores complexos — 1 µop/instr. em **98%** das vezes, melhorado vs. 96%) |
| Alocação/renomeamento | 2 (→ ROB + PRF) |
| Agendamento | 1 |
| Acesso cache dados | 3 (apenas se instrução precisa de memória): AG + DC + DC |
| Execução | 1+ |
| Reordenamento | 4: verificação falhas × 2 + desfaz renomeamento + write-back |

### Novidades Silvermont vs. Bonnell

- 2ª unidade de previsão de desvios (ativada pelo decodificador, associada à execução especulativa)
- **LSD** (Loop Stream Detector) adicionado à fila de instruções
- Fila de instruções: **32 entradas** (unificada, sem HT)
- AGU opera **fora de ordem** (antes: em ordem no Bonnell)
- **Fila de reabilitação**: instrução de memória problemática é deslocada para esta fila; próxima instrução procede; problemática despachada quando resolvida

### Características Silvermont

| Atributo | Valor |
|----------|-------|
| L1-I | 32 KiB (8 vias) |
| L1-D | 24 KiB (6 vias) |
| L2 | Compartilhado por módulo (2 núcleos); tamanho depende do modelo |
| HT | Removido |
| SSE | SSE4.1, SSE4.2 (exceto SoC — adicionado no Airmont), AESNI, CLMUL |
| TXT/SMX | Suportado (hardware integrado) |
| DMI | 2.0 |
| GPU Silvermont | Gen7, DirectX 11 |
| GPU Airmont | Gen8, DirectX 11.2 |
| GPU SoFIA | Mali 400 MP2 ou Mali 450 MP4 |

### Agendadores separados por tipo (Silvermont)

3 agendadores independentes: **inteiros**, **ponto flutuante**, **memória** (vs. agendador unificado na P6).

### Unidades de Execução — Silvermont (2 portas)

| Porta | Unidades |
|-------|---------|
| 0 | ALU 0, SIMD ALU, LEA, SHIFT, AGU, DESVIOS |
| 1 | ALU 1, SIMD ALU, SHUFFLE, FP ADD, FP MUL/DIV/CVT, MUL, STTNI/AESNI/CLMUL, RCP/RSQRT, F2I, POPCNT, CRC32, PROC. BITS |

---

## Microarquitetura Goldmont (2016)

Expansão do Silvermont → **3 µops/ciclo** (decodificação, envio, alocação).

### Novidades vs. Silvermont

- Cache L2 de pré-decodificação: **16 KiB** (marcadores de instrução armazenados no L2 também)
- **3º decodificador** de instruções; analisa **20 bytes/ciclo** (antes: 16)
- Envio e alocação: **3 µops/ciclo** (antes: 2)
- Agendadores FP unificados (antes: 2 separados no Silvermont)
- **3º agendador INT** + **3ª porta de despacho** + **3ª ALU inteiros**
- Agendador memória: pode despachar para **portas 0 e 1** (antes: só porta 0) → carga e armazenamento simultâneos
- AGU fora de ordem (antes: em ordem no Silvermont); fila de reabilitação removida → substituída por buffers de carga/armazenamento
- Acesso L1-I independente da unidade de busca
- FP ADD: 128 bits para **todas** as instruções (antes: só ADDPS/SUBPS)
- **MPX, RDRAND, RDSEED, SHANI**
- **SMEP e SMAP**
- GPU: Gen9, DirectX 12

### Unidades de Execução — Goldmont (3 portas)

| Porta | Unidades |
|-------|---------|
| 0 | ALU 0, SIMD ALU, LEA, SHIFT |
| 1 | ALU 1, SIMD ALU, SHUFFLE, SIMD SHUFFLE, LEA, SIMD MUL, FP ADD, FP MUL, DIV, F2I CMP, FLAG MERGE, CONV/CMP, STTNI/AESNI/CLMUL/SHANI, PROC.BITS, POPCNT, CRC32, I2F, F2I, F2I CONV, MUL, I2F |
| 2 | AGU carga; AGU armazenamento + dados armazenamento |

Novidades de unidades:
- **I2F**: conversão inteiro → FP
- **FLAG MERGE**: funde flags das instruções INC/DEC (µop extra)
- **CONV/CMP**: conversão e comparação

---

## Microarquitetura Goldmont Plus (2017)

Otimização da Goldmont. Novidades:

- Fila para ponteiro de instruções adicionada
- **4 µops/ciclo** de alocação (decodificadores: ainda 3)
- **4ª porta de despacho** exclusiva para unidade de desvios → agendador de desvios dedicado
- Cache L2 pré-decodificação: 16 → **64 KiB**
- Algoritmo de divisão: **radix-1024** (menos ciclos por divisão)
- Módulo passa a ter **4 núcleos** (antes: 2); L2 de **4 MiB** (16 vias) compartilhado
- **SGX** suportado
- GPU: Gen9.5 (mesmo do Kaby Lake)

### Unidades de Execução — Goldmont Plus (4 portas)

Igual Goldmont + **porta 3 exclusiva para desvios**.

---

## Microarquitetura Tremont (2020)

Novidades em relação à Goldmont Plus:

- **Blocos de entrada duplicados**: 2 filas de instruções + 2 decodificadores (3 instr./ciclo cada) → **6 instruções x86/ciclo** decodificadas (alocação: ainda 4 µops/ciclo)
- ROB: **208 entradas**
- Portas de despacho: **individuais** (antes: compartilhadas) → **10 portas**
- Numeração portas: 1º dígito = tipo (0=int, 1=memória, 2=FP)
- L1-D: 24 → **32 KiB** (8 vias)
- Cache L2 pré-decodificação: 64 → **128 KiB**
- L2 por módulo: **1,5 MiB** (12 vias); modelos rádio base: 4,5 MiB (18 vias)
- **L3 de 4 MiB** (12 vias, LLC) compartilhado entre todos os módulos nos modelos Celeron/Pentium Silver (não disponível nos Atom)
- GPU: Gen11 (mesmo do Sunny Cove)
- **RDT** (Resource Director Technology): priorização de acesso a caches e RAM por QoS
- **GFNI** (Galois Field New Instructions) — como SSE (sem AVX)
- **TME** (Total Memory Encryption)
- **Speed Shift**

### Unidades de Execução — Tremont (10 portas individuais)

| Porta | Tipo | Unidades |
|-------|------|---------|
| 00, 01, 02 | Inteiros | ALU 0, ALU 1, ALU 2; LEA, SHIFT, MUL, DIV, DESVIOS (na 08) |
| 08 | Inteiros | DESVIOS |
| 10, 11 | Memória | AGU carga × 2; AGU armazenamento × 2; dados armazenamento |
| 20, 21 | FP | SIMD ALU × 2, AESNI × 2, FP MUL/SHA-RND, SHA-MSG, FP DIV, FP ADD/SHUFFLE, SHIFT, PROC.BITS, POPCNT, CRC32, SHUFFLE, SIMD MUL, GFNI, CONV |
| 29 | FP | dados armazenamento FP |

Novidades de unidades:
- **SHA-RND e SHA-MSG** separados (antes: SHANI numa única unidade junto com AESNI)
- **GFNI**: unidade exclusiva

Despacho máximo teórico: **10 µops/ciclo** (vs. 4 no Goldmont Plus) — aumento de 150%.

---

## Microarquitetura Gracemont (2021)

Expansão significativa da Tremont. Novidades:

- **OD-ILD** (On-Demand Instruction Length Decoder): identifica comprimento e armazena de volta no L1-I (by-pass na re-busca — instrução já conhecida não re-identifica comprimento)
- **5 µops/ciclo** de alocação (antes: 4)
- **17 portas de despacho** (antes: 10); 1º dígito = tipo (0=int, 1=memória, 2=FP, 3=desvios)
- **4ª ALU inteiros** + **3ª ALU vetorial**
- ROB: **256 entradas** (antes: 208)
- L1-I: 32 → **64 KiB** (8 vias)
- L2 por módulo: **2 MiB ou 4 MiB**
- DDR5 e PCIe 4.0
- GPU: Gen12 (Intel Xe), DirectX 12.1
- **AVX2, FMA3, AVX-VNNI** (256 bits) — primeira µarq Atom com suporte a AVX
- **VT-rp** (virtualização com proteção contra redirecionamento)

### Unidades de Execução — Gracemont (17 portas)

| Portas | Tipo | Unidades |
|--------|------|---------|
| 00, 01, 02, 03 | Inteiros | ALU × 4, SHIFT × 4, MUL × 2, DIV × 2 |
| 08, 09 | Inteiros | — |
| 10, 11 | Memória | AGU carga × 2 |
| 12, 13 | Memória | AGU armazenamento × 2 + dados armazenamento × 2 |
| 20, 21 | FP | VEC ALU × 2, AESNI × 2, FP MUL × 2, FP ADD × 2 |
| 22 | FP | VEC ALU + SIMD MUL + FP DIV + SHA |
| 28, 29 | FP | dados armazenamento × 2 |
| 30, 31 | Desvios | DESVIOS × 2 |

---

## Evolução das Estruturas Internas (Resumo)

| Microarquitetura | Execução | HT | Portas | Decodificadores | µops alocação/ciclo | L1-I | L1-D | ROB |
|-----------------|---------|-----|--------|----------------|-------------------|------|------|-----|
| Bonnell | Em ordem | Sim | 2 | 2 complexos | 2 | 32 KiB | 24 KiB | — |
| Silvermont | OoO | Não | 2 | 2 complexos | 2 | 32 KiB | 24 KiB | — |
| Goldmont | OoO | Não | 3 | 3 | 3 | 32 KiB | 24 KiB | — |
| Goldmont Plus | OoO | Não | 4 | 3 | 4 | 32 KiB | 24 KiB | — |
| Tremont | OoO | Não | 10 | 2×3 (6 total) | 4 | 32 KiB | 32 KiB | 208 |
| Gracemont | OoO | Não | 17 | 2×3 (6 total) | 5 | 64 KiB | 32 KiB | 256 |

---

## Processadores

Todos usam BGA (soldados diretamente na placa). Nenhum tem soquete.

| Série | Mercado | Observação |
|-------|---------|-----------|
| Atom | Embarcado, tablet, rádio base | Nome original; várias séries por mercado |
| Celeron | Baixo custo (portátil/desktop) | Nome compartilhado com família 6 P/Core |
| Pentium / Pentium Silver | Baixo custo (portátil/desktop) | Nome compartilhado com família 6 P/Core |
| Core i (híbrido) | Mainstream/portátil | Núcleos E Gracemont + núcleos P Golden Cove |

Modelos do tipo **SoC** integram USB, SATA, rede, processador de imagens, cartão de memória etc. diretamente no chip — variam por modelo.

---

## Ver também

- [[Microarquitetura Intel Família 6 Parte 3]] — núcleos P (Golden Cove, Raptor Cove); híbridos Alder/Raptor Lake
- [[Processadores Intel Híbridos]] — arquitetura P+E: Lakefield/Alder/Raptor Lake; restrições instruções (AVX-512 bloqueado pelo Gracemont)
- [[Desempenho do Processador]] — execução OoO, PRF, HT, execução superescalar, LSD
- [[Cache]] — L2 compartilhado por módulo (Silvermont+); L3 LLC Tremont; OD-ILD Gracemont
- [[Gerenciamento de Consumo Elétrico]] — SpeedStep (Bonnell), Speed Shift (Tremont+), TDP ≤ 10 W
- [[Máquina Virtual]] — VT-x (Bonnell opcional), VT-rp (Gracemont)
- [[Instruções SIMD]] — AVX2/FMA3 primeira vez na família Atom (Gracemont), GFNI (Tremont)
- [[Instruções Adicionais x86]] — RDRAND/RDSEED/SHANI/MPX (Goldmont), SGX (Goldmont Plus), GFNI/TME (Tremont)
- [[Fabricação de Chips]] — Bonnell 45nm → Gracemont 10nm Intel 7; BGA para todos os modelos
