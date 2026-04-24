---
title: Processadores AMD — Família Hammer (K8, K10, K10.5)
tags:
  - hardware
  - arquitetura-x86
  - história
  - processadores
  - amd
  - 64-bits
aliases:
  - K8
  - K10
  - K10.5
  - Hammer
  - Athlon 64
  - Phenom
  - Opteron
  - Turion
  - família 15 AMD
  - família 16 AMD
  - família 18 AMD
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Processadores AMD — Família Hammer (K8, K10, K10.5)

Período: **2003–2012**. Três microarquiteturas: K8 (família F/15), K10 (família 10h), K10.5 (família 12h, informal).

> [!note] Marcos históricos desta família
> - K8: primeiro x86 de **64 bits** e primeiro com **controlador de memória integrado**
> - K10: primeiras instruções SSE4a e HyperTransport 3.0
> - K10.5: primeira APU (CPU + GPU num mesmo chip) da AMD

---

## Soquetes

| Soquete | Pinos | Uso principal | Memória |
|---------|-------|--------------|---------|
| 754 | 754 | Athlon 64 entrada de nível | single-channel DDR/DDR2 |
| 939 | 939 | Athlon 64 / FX / X2 | dual-channel DDR |
| 940 | 940 | Opteron / Athlon 64 FX iniciais | dual-channel DDR (ECC) |
| AM2 | 940 (diferente) | Athlon 64 / X2 / FX / Sempron | dual-channel DDR2 |
| AM2+ | 940 | Phenom / Athlon X2 | dual-channel DDR2 (HT 3.0) |
| AM3 | 938 | Phenom II / Athlon II / Sempron | dual-channel DDR3 |
| FM1 | 905 | Série A / Série E (K10.5 APU) | dual-channel DDR3 |
| C32 | 1207 | Opteron entrada servidor | dual-channel DDR3 |
| G34 | 1944 | Opteron multi-socket (até 4×) | quad-channel DDR3 |
| F | 1207 | Opteron K8 servidor | dual-channel DDR/DDR2 |

---

## HyperTransport

Barramento ponto-a-ponto serial que substituiu o FSB externo. Largura: **16 bits** (bidirecional); DDR (2 transferências/pulso).

| Versão | MT/s | Banda (16 bits, unidirecional) |
|--------|------|-------------------------------|
| HT 1.x | 800 MT/s | 1.600 MB/s |
| HT 2.0 | 1.400 MT/s | 2.800 MB/s |
| HT 3.0 | 2.600 MT/s | 5.200 MB/s |
| HT 3.1 | 3.200 MT/s | 6.400 MB/s |

> [!note] Notação MT/s
> HyperTransport é DDR: "800 MHz" = 400 MHz real = **800 MT/s**. Mesma convenção do FSB DDR do K7. Ver [[Processadores AMD Família K7]].

---

## Microarquitetura K8

CPUID família **F** (15 decimal). Introduzido em 2003 com o Opteron/Athlon 64.

### Inovações principais

| Recurso | Detalhe |
|---------|---------|
| **64 bits** | Primeira extensão 64 bits do x86 — AMD64 (EM64T da Intel veio depois) |
| **Controlador de memória integrado** | Primeiro no x86; elimina latência do chip norte |
| **HyperTransport** | Substitui FSB externo; ponto-a-ponto; múltiplos canais |
| **AMD-V** | Virtualização assistida por hardware |
| SSE2 + SSE3 | Suporte completo |

### Macroinstruções

K8 classifica cada instrução x86 antes de decodificar:

| Tipo | Significado | Caminhos de decodificação |
|------|------------|--------------------------|
| **DirectPath Single** | 1 µop | Decodificação direta rápida |
| **DirectPath Double** | 2 µops | Decodificação direta dupla |
| **VectorPath** | 3+ µops | Microcódigo; mais lento |

Instruções buscadas em **linhas de 3 macroinstruções** por ciclo.

### Pipeline e unidades de execução

- Pipeline inteiro: **12 estágios** (base) / **17 estágios** (com penalidade de branch misprediction)
- Renomeamento de registradores: **IFFRF** (Integer Fast Forward Register File)
- Unidades de execução:
  - **3 ALU** inteiras
  - **3 AGU** (Address Generation Units)
  - **FADD** (ponto flutuante adição)
  - **FMUL** (ponto flutuante multiplicação)
  - **FMISC** (ponto flutuante miscelânea)

### Problema do divisor de memória (K8)

K8 com controlador de memória integrado exige que o clock de memória seja **divisor inteiro** do clock do processador. Quando o clock do núcleo não é múltiplo exato do clock da memória, há arredondamento → DDR2-800 efetiva pode cair para DDR2-711 ou similar. **Corrigido no K10** com um divisor fracionário mais preciso.

---

## Microarquitetura K10

CPUID família **10h** (16 decimal). Introduzida em 2007 com o Phenom.

### Melhorias sobre K8

| Recurso | K8 | K10 |
|---------|-----|-----|
| Busca de instruções | 16B/ciclo | **32B/ciclo** |
| Caminhos FP | 64 bits | **128 bits** (Wide FP Accelerator) |
| DirectPath Double | Sim | **Removido** (melhorado em DirectPath Single) |
| SSE4a | Não | **Sim** |
| Virtualização | AMD-V | **AMD-V + RVI** (Rapid Virtualization Indexing) |
| Turbo | Não | **Turbo Core** (boost automático) |
| L3 | Não | Opcional (depende do modelo) |
| HyperTransport | HT 2.0 | **HT 3.0 / 3.1** |
| Divisor memória | Problema | **Corrigido** |

### Wide FP Accelerator

Caminhos de ponto flutuante aumentados de 64 para **128 bits** — equivalente a processar duas operações SSE de 64 bits por ciclo em vez de uma. Denominação AMD para esta otimização.

### DDPM (Dual Dynamic Power Management)

Plano de alimentação **dividido** entre núcleos e unidade norte integrada (NB — North Bridge). Permite ajuste de tensão independente, reduzindo consumo.

### ABM (Advanced Bit Manipulation)

Unidade dedicada para instruções de contagem de bits (POPCNT, LZCNT). Incluída no conjunto SSE4a (exclusivo AMD até Intel Nehalem).

---

## Microarquitetura K10.5

Classificação **informal** (alguns autores chamam de Bobcat predecessor; AMD não usa esse nome). CPUID família **12h** (18 decimal).

### Diferenças sobre K10

| Recurso | Detalhe |
|---------|---------|
| ROB | **28 linhas × 3 entradas = 84 µops** (maior que K10) |
| Escalonadores | Maiores que K10 |
| Divisão inteira | Unidade **INT DIV** dedicada (K10 usava microcódigo) |
| GPU integrada | **TeraScale 2** (DirectX 11) — somente APUs |
| PCIe | **PCIe 2.0** |
| Interconexão | **UMI** substitui HyperTransport (em APUs FM1) |
| Soquete | **FM1** (APUs) |

> [!note] APU — Accelerated Processing Unit
> K10.5 na Série A (codinome Llano) integra CPU K10.5 + GPU TeraScale 2 no mesmo chip. AMD cunhou o termo APU para esse produto. Primeiro APU x86 do mercado.

---

## Processadores Desktop

### Athlon 64 — 2003–2007 (K8)

Primeiro processador K8 para consumidor. Canal único (soquete 754) ou duplo (soquete 939/940/AM2).

| Codinome | Processo | Núcleos | Soquete | Novidade |
|---------|----------|---------|---------|---------|
| ClawHammer | 130 nm | 1 | 754/939 | Primeiro Athlon 64 |
| Newcastle | 130 nm | 1 | 754/939 | Die menor |
| Winchester | 90 nm | 1 | 939 | 90 nm |
| Venice | 90 nm | 1 | 939/AM2 | SSE3 |
| San Diego | 90 nm | 1 | 939 | L2 1 MiB, SSE3 |
| Toledo | 90 nm | 2 | 939 | Dual-core (2 núcleos K8) |
| Manchester | 90 nm | 2 | 939 | Dual-core L2 menor |
| Orleans | 90 nm | 1 | AM2 | DDR2 |
| Lima | 65 nm | 1 | AM2 | 65 nm |

### Athlon 64 X2 — 2005–2008 (K8)

Versão dual-core do Athlon 64. Soquetes 939, AM2.

### Athlon X2 — 2007–2009 (K8/K10)

Versão de entrada do dual-core. Inclui modelos baseados em K8 e modelos baseados em K10.

### Athlon 64 FX — 2003–2006 (K8)

Versão de alto desempenho / entusiastas. Multiplicador desbloqueado. Soquetes 939, 940 (inicial).

### Phenom X3 / X4 — 2007–2008 (K10)

Primeiro processador K10 para consumidor. X3 = tri-core; X4 = quad-core. Soquete AM2+.

> [!warning] TLB Bug (Phenom B2)
> Revisão B2 do Phenom tinha bug no TLB que causava instabilidades. Corrigido na revisão B3 com penalidade de desempenho (patch via BIOS). Phenom II não tem o bug.

### Phenom II — 2009–2011 (K10)

Phenom revisado em 45 nm. Soquetes AM2+/AM3. L3 incluído em todos os modelos.

| Codinome | Núcleos | L3 | Soquete |
|---------|---------|-----|---------|
| Deneb | 4 | 6 MiB | AM2+/AM3 |
| Thuban | 6 | 6 MiB | AM3 |
| Callisto | 2 | 6 MiB | AM2+/AM3 |
| Heka | 3 | 6 MiB | AM2+/AM3 |
| Zosma | 4 | 6 MiB | AM3 |
| Lucifer | 6 | sem L3 | AM3 |

### Athlon II — 2009–2011 (K10/K10.5)

Versão de entrada do Phenom II — **sem L3**. Soquetes AM2+/AM3/FM1.

### Sempron (K8/K10.5) — vários anos

Versões de entrada. K8 (soquetes 754/AM2) e K10.5 (FM1, sem GPU ou GPU básica).

### Série A — 2011 (K10.5 APU)

APU: CPU K10.5 + GPU TeraScale 2 integrada. Codinome **Llano**. Soquete FM1.

| Modelo | Núcleos CPU | GPU |
|--------|------------|-----|
| A8 | 4 | Radeon HD 6550D |
| A6 | 4 (2 desabilitados) | Radeon HD 6530D |
| A4 | 2 | Radeon HD 6410D |

### Série E — 2011 (K10.5)

Baixo consumo. Com GPU (E-350/E-450, codinome **Zacate**) ou sem GPU (E2/E-300, codinome **Ontario**).

> [!warning] Zacate e Ontario são Bobcat, não K10.5
> Capítulo 31 esclarece: Zacate e Ontario são microarquitetura **Bobcat (família 14h)**, 40 nm — não K10.5. A designação "Série E" foi reutilizada em múltiplas µarqs. Ver [[Processadores AMD Família Bobcat]].

---

## Processadores Portáteis

### Mobile Athlon 64 — 2003–2006 (K8)

Athlon 64 para notebooks. Soquete 754/S1.

### Mobile Sempron — 2004–2008 (K8)

Sempron para notebooks de entrada.

### Athlon XP-M "Dublin" — 2004 (K8)

> [!warning] Dublin é K8, não K7
> Apesar do nome "Athlon XP-M", o codinome Dublin é baseado no **K8** (não no K7). Pode causar confusão com o Athlon XP-M K7. Ver [[Processadores AMD Família K7]].

### Turion 64 — 2005–2006 (K8)

Linha premium para notebooks. Competia com Pentium M / Core Duo.

### Turion 64 X2 / Turion X2 — 2006–2008 (K8)

Versão dual-core do Turion. Nomenclatura simplificada: Turion X2 (sem "64") a partir de 2007.

### Turion X2 Neo / Turion Neo — 2009 (K8/K10)

Turion de ultrabaixo consumo (ULV). Competia com Intel Atom/CULV.

### Athlon Neo / Athlon Neo X2 — 2009 (K8)

Versão ainda mais barata do Turion Neo. Soquete S1.

### Athlon II Neo — 2010 (K10)

Athlon II em versão ULV para notebooks ultrafinos.

### Phenom II portátil — 2009–2011 (K10)

Phenom II adaptado para notebooks. Soquete S1.

### Turion II — 2009–2010 (K10)

Turion baseado em K10. Soquete S1.

### Série A / E portátil — 2011 (K10.5)

APU Llano/Zacate/Ontario em versão portátil.

---

## Processadores para Servidores

### Opteron — 2003–2012

Primeiro processador Opteron: codinome **SledgeHammer** (K8, 130 nm, soquete 940, 2003).

Numeração dos modelos: **1º dígito indica suporte SMP máximo**.

| Prefixo | SMP máximo | Exemplo |
|---------|-----------|---------|
| 1xx | 1 socket | Opteron 144 |
| 2xx | 2 sockets | Opteron 252 |
| 4xx | 4 sockets | Opteron 452 |
| 6xx | 4 sockets (MCM) | Opteron 6174 |
| 8xx | 8 sockets | Opteron 852 |

| Geração | Codinome | µarq | Soquete | Processo |
|---------|---------|------|---------|---------|
| 1ª | SledgeHammer | K8 | 940 | 130 nm |
| 1ª (rev.) | IronGate | K8 | 940 | 130 nm |
| 2ª | Egypt/Italy | K8 | 940 | 90 nm |
| 3ª (AM2) | Santa Ana/Rosa | K8 | F | 90 nm |
| 4ª | Budapest/Barcelona | K10 | F/C32 | 45 nm |
| 5ª (6-core) | Istanbul | K10 | F/C32 | 45 nm |
| 6ª (MCM) | Magny-Cours | K10 (2 dies) | G34 | 45 nm |

> [!note] Magny-Cours (Opteron 6xxx)
> MCM com **dois dies K10** de 6 núcleos cada = 12 núcleos por pacote. Competia com Xeon E7. Soquete G34 (1944 pinos).

### V-Series — 2009 (K8)

Opteron para aplicações embarcadas / baixo consumo. Soquete C32.

---

## Tabela Comparativa µarqs

| Recurso | K8 | K10 | K10.5 |
|---------|-----|-----|-------|
| CPUID família | F (15) | 10h (16) | 12h (18) |
| 64 bits | Sim | Sim | Sim |
| Busca | 16B/ciclo | 32B/ciclo | 32B/ciclo |
| Caminhos FP | 64 bits | 128 bits | 128 bits |
| SSE | SSE2/SSE3 | SSE4a | SSE4a |
| AVX | Não | Não | Não |
| L3 | Não | Opcional | Opcional |
| GPU integrada | Não | Não | Sim (APUs) |
| Virtualização | AMD-V | AMD-V + RVI | AMD-V + RVI |
| HyperTransport | 1.x/2.0 | 3.0/3.1 | UMI (APUs) |
| Divisor memória | Problema | Corrigido | Corrigido |
| Processo | 130–65 nm | 65–45 nm | 32 nm |

---

## Ver também

- [[Processadores AMD Família Bulldozer]] — sucessores: Bulldozer/Piledriver/Steamroller/Excavator (família 15h)
- [[Processadores AMD Família K7]] — predecessores: Athlon, Athlon XP, Duron
- [[Microarquitetura Netburst e Intel Família F]] — concorrente Intel (Pentium 4, Pentium D)
- [[Microarquitetura Intel Família 6 Parte 3]] — concorrente Intel (Core 2, Core i)
- [[Instruções SIMD]] — SSE2/SSE3/SSE4a/AVX2 mencionados nesta família
- [[Cache]] — L3 introduzido no Opteron K10; APU compartilha L2 entre CPU e GPU
- [[Fabricação de Chips]] — evolução 130→90→65→45→32 nm
- [[Desempenho do Processador]] — controlador de memória integrado, HyperTransport, Wide FP Accelerator
