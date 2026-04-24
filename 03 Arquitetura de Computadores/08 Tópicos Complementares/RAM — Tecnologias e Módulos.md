---
title: RAM — Tecnologias e Módulos
aliases:
  - DRAM
  - SDRAM
  - DDR
  - LPDDR
  - GDDR
  - HBM
  - memória dinâmica
  - memória estática
tags:
  - computação/hardware
  - computação/arquitetura
date: 2026-04-12
source: "Livro Hardware clubedohardware.com.br — Cap. 33"
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# RAM — Tecnologias e Módulos

> **RAM** (Random Access Memory) = memória de leitura **e** escrita, volátil, acesso aleatório.  
> Nome tecnicamente correto seria **RWM**; a contraparte **ROM** também é de acesso aleatório.

→ Conceito base: [[Memória]]  
→ Posição na pirâmide: [[Hierarquia de Memória]] (L4 = DRAM principal)  
→ Cache usa SRAM: [[Cache]]

---

## Relações (SPO)

- RAM → implementa → memória principal (CPU) e memória de vídeo (GPU)
- SRAM → usa → flip-flop (4–6 transistores/bit)
- DRAM → usa → capacitor + MOSFET (1 transistor/bit)
- DRAM → requer → refresh periódico (capacitor descarrega)
- SDRAM → sincronizada por → sinal CK
- DDR → transfere → 2 dados por pulso de clock (borda ↑ e ↓)
- LPDDR → voltada a → dispositivos portáteis (menor tensão)
- GDDR → voltada a → memória de vídeo (GPU)
- HBM → integrada em → encapsulamento do processador (TSV)

---

## SRAM vs DRAM

| Característica | SRAM | DRAM |
|---|---|---|
| Célula | Flip-flop | Capacitor + MOSFET |
| Velocidade | Alta | Baixa |
| Densidade | Baixa | Alta |
| Custo | Alto | Baixo |
| Refresh | Não | Sim |
| Uso | Cache interno do processador | Memória principal |

**Célula SRAM:** Word Line (WL) + par B/B̄ + amplificador (comparador de tensão). Implementações com 4 ou 6 transistores MOSFET.  
**Célula DRAM:** 1 MOSFET + 1 capacitor. Bit Line (BL) compartilhada por coluna; Word Line (WL) compartilhada por linha. Capacitor carregado = "1"; descarregado = "0".

---

## SDRAM — Arquitetura Interna

Organização interna em **bancos** (matrizes de capacitores). Chip típico: 4 bancos.

### Pinos

| Grupo | Pinos | Função |
|---|---|---|
| Dados | DQ0…DQn | Barramento de dados do chip |
| Endereço linha/coluna | A0…An | Linha ou coluna a acessar |
| Endereço banco | BA0…BAn | Banco (matriz) a acessar |
| Clock | CK, CK̄ | Transmissão diferencial |
| Clock enable | CKE | Ativa/desativa leitura de clock |
| Terminação | ODT | On-Die Termination (a partir de DDR2) |
| Reset | RESET | Reinicia memória (a partir de DDR3) |
| Comandos | CS, RAS, CAS, WE | 4 pinos → barramento de comandos |

> A partir de **DDR4**: RAS, CAS, WE compartilhados com A14–A16; pino **ACT** distingue endereço de comando.  
> A partir de **DDR5 / LPDDR2 / GDDR6**: barramento de comandos e endereços **totalmente combinado**.

### Principais Comandos

| Comando | Ação |
|---|---|
| ACTIVATE (ACT) | Abre linha para leitura/escrita |
| READ / RDAP | Lê célula (RDAP fecha linha após) |
| WRITE / WRAP | Grava célula (WRAP fecha linha após) |
| PRECHARGE (PRE/PREA) | Fecha linha (banco ou todos) |
| AUTO REFRESH (REF) | Ciclo de refresh |
| SELF REFRESH (REFS) | Modo economia (timer interno comanda refresh) |
| LOAD MODE (MRS) | Configura memória |
| NO OPERATION (NOP) | Sem operação |

### Ciclo de Acesso (tRCD)

1. Controlador envia **ACTIVATE** + endereço de linha + banco
2. Aguarda **tRCD** pulsos de clock
3. Envia **READ** ou **WRITE** + endereço de coluna
4. Dado disponível após **latência de leitura (RL = CL + AL)**
5. Linha permanece aberta → acessos subsequentes à mesma linha **sem novo ACTIVATE**
6. **PRECHARGE** fecha a linha ao final

### Temporizações (formato: CL-tRCD-tRP-tRAS)

| Parâmetro | Significado |
|---|---|
| CL (CAS Latency) | Pulsos entre READ e dado disponível |
| tRCD | Pulsos entre ACTIVATE e READ/WRITE |
| tRP | Pulsos entre PRECHARGE e próximo ACTIVATE |
| tRAS | Pulsos mínimos entre ACTIVATE e PRE/leitura/escrita |

> Comparação entre memórias de **clocks diferentes**: converter latência em tempo absoluto = CL / f_efetivo.  
> Ex: DDR4-3200 CL16 → 16 / 3,2×10⁹ = **5 ns** < DDR4-2133 CL15 → 15 / 2,133×10⁹ = **7 ns**

### Modo Rajada (Burst)

Contador interno seleciona colunas consecutivas automaticamente, sem novo READ.  
Rajada: **sequencial** (mesma linha, mesmo banco) ou **intercalada** (alterna bancos).  
- SDRAM/DDR1: rajada 1, 2, 4, 8 ou página inteira
- DDR2+: rajada 4 ou 8
- DDR4+: rajada 8 ou 16 (DDR5: 16 ou 32)

---

## DDR — Gerações

**DDR (Double Data Rate):** 2 dados por pulso de clock (borda ↑ + borda ↓).  
Clock divulgado = clock **efetivo** (2× clock real). Correto em MT/s, não MHz.

| Memória | Mínimo | Máximo | VDD | Pré-busca | Bancos |
|---|---|---|---|---|---|
| DDR1 | 200 MT/s | 400 MT/s | 2,5–3,3 V | 2n | 4 |
| DDR2 | 400 MT/s | 1.066 MT/s | 1,8 V | 4n | 4 ou 8 |
| DDR3 | 800 MT/s | 2.133 MT/s | 1,5 V (DDR3L: 1,35 V) | 8n | 8 |
| DDR4 | 1.600 MT/s | 3.200 MT/s | 1,2 V | 8n | 4/grupo × 4 grupos |
| DDR5 | 4.800 MT/s | 8.800 MT/s | 1,1 V | 16n | 2–4/grupo × 4–8 grupos |

**Pré-busca Xn:** transfere X dados de n bits dos bancos para os buffers I/O por pulso de clock interno.

### DDR2
- ODT migra para dentro do chip (antes ficava na placa-mãe)
- Pré-busca 4n

### DDR3
- Pré-busca 8n
- DDR3L: 1,35 V compatível com 1,5 V (inverso não vale)

### DDR4
- Pré-busca 8n; desempenho via **grupos de bancos** (acesso paralelo)
- Combinação parcial barramento CMD+END (A14–A16 compartilhados com RAS/CAS/WE; pino ACT)
- CRC em escrita (8 bits verificação / 72 bits); opcional; não corrige, apenas detecta → retransmite

### DDR5
- Pré-busca 16n; mais grupos de bancos
- Barramento CMD+END totalmente combinado
- CRC em leitura **e** escrita
- **ECC no chip** (SEC, 8 bits/128 bits): corrige 1 bit; independente do módulo ECC
- PMIC opcional no módulo (regulador de tensão integrado)
- Barramento de dados dividido em **2 canais de 32 bits** por módulo

---

## LPDDR — Gerações

Soldadas diretamente na placa-mãe. Não estão disponíveis em módulos.  
Gerações **não correspondem** às DDR (ex: LPDDR4 tem pré-busca 16n; DDR só atingiu isso no DDR5).

| Memória | Clock máx | Pré-busca | Bancos | ODT | ECC link |
|---|---|---|---|---|---|
| LPDDR1 | 400 MT/s | 2n | 4 | Não | Não |
| LPDDR2 | 1.066 MT/s | 2n ou 4n | 4–8 | Não | Não |
| LPDDR3 | 2.133 MT/s | 8n | 8 | Sim | Não |
| LPDDR4 | 4.266 MT/s | 16n | 8 | Sim | Não |
| LPDDR4X | 4.266 MT/s | 16n | 8 | Sim | Não |
| LPDDR5 | 6.400 MT/s | 16n | 4–16 | Sim | Sim (SEC) |
| LPDDR5X | 8.533 MT/s | 16n | 16 | Sim | Sim (SEC) |

### Clock LPDDR5
- CK ≤ 800 MHz; WCK e RDQS = 2× ou 4× CK
- Dados: DDR em relação a WCK → 4× ou 8× em relação a CK
- CMD/END: DDR em relação a CK
- **DVS/DVFS**: tensões e frequência reduzem com barramento ocioso

### Barramento CMD+END por geração
- SDR: LPDDR1, LPDDR4, LPDDR4X
- DDR: LPDDR2, LPDDR3, LPDDR5, LPDDR5X

---

## GDDR — Gerações

Exclusivas para memória de vídeo. **Não correspondem** às DDR (GDDR5 lançada 2007 = era do DDR3).

| Memória | Dados/CK | WCK:CK | Notas |
|---|---|---|---|
| GDDR1–4 | 2× (DDR) | — | Apenas CK; similar DDR1–DDR3 |
| GDDR5 | 4× (QDR) | 2:1 | WCK intro; END em DDR; CRC via EDC |
| GDDR5X modo DDR | 4× (QDR) | 2:1 | = GDDR5 |
| GDDR5X modo QDR | 8× (ODR) | 2:1 | 4 dados/WCK |
| GDDR6 modo QDR WCK | 8× (ODR) | 2:1 | CMD+END combinados; 2 canais/chip (DC) |
| GDDR6 modo DDR WCK | 8× (ODR) | 4:1 | Menor consumo |
| GDDR6X modo PAM4 | 8× (ODR) | 2:1 | Codificação PAM4: 2 bits/símbolo |
| GDDR6X modo RDQS | 8× (ODR) | 4:1 | NRZ; menor velocidade |

### GDDR5 — Novidades
- Segundo clock **WCK** (sincroniza dados); CK para CMD/END
- END: SDR para CMD, **DDR** para endereços
- **CRC via barramento EDC separado** → sem perda de largura de banda (diferente de DDR4/DDR5)
- **Modo clamshell**: chip 32 bits opera em 16 bits → 2 chips por canal → dobra capacidade

### GDDR6X — PAM4
- 4 níveis de tensão → 2 bits/símbolo  
- Mesma largura de banda em símbolos (baud) = **dobro** de dados vs GDDR6 em mesma freq  
- Fabricante único: Micron (não é padrão JEDEC)

### Larguras de banda GDDR (exemplos de mercado)

| Memória | Mínimo | Máximo |
|---|---|---|
| GDDR5 | 6 GT/s | 8 GT/s |
| GDDR5X | 10 GT/s | 12 GT/s |
| GDDR6 | 10 GT/s | 16 GT/s |
| GDDR6X | 19 GT/s | 21 GT/s |

---

## HBM — High-Bandwidth Memory

Integrada no mesmo encapsulamento da CPU/GPU. Conexão via **TSV** (Through-Silicon Vias) + microbumps + interposer.

| Característica | HBM1 | HBM2 | HBM2E |
|---|---|---|---|
| Barramento/canal | 128 bits + ECC | 128 bits + ECC | 128 bits + ECC |
| Canais/pilha | até 8 | até 8 | até 8 |
| Pré-busca | 2n | 2n | 2n |
| Clock CK | 500 MHz | 1–1,2 GHz | 1,4–1,8 GHz |
| Clock efetivo | 1 GT/s | 2–2,4 GT/s | 2,8–3,6 GT/s |
| Capacidade/pilha | 1 GiB | até 8 GiB | até 16 GiB |

- Apenas CK; dados em DDR
- Modo **Pseudo Channel (PC)**: canal 128 bits → 2 × 64 bits independentes (HBM2/2E)
- ECC opcional
- Sem ODT (integrada no pacote)
- Sem CRC (caminho curto, sem necessidade)
- Largura de banda total = clock efetivo × 1.024 bits / 8 × nº de pilhas  
  Ex: HBM2E 3,2 GT/s × 4 pilhas = **1,64 TB/s**

> Conexão com [[Microarquitetura Intel Família 6 Parte 3]]: Golden Cove servidor usa HBM2E  
> Conexão com [[Processadores AMD Família Zen]]: EPYC usa HBM em versões HPC

---

## Densidade e Organização

Chips anunciados em **bits** (Gibit); módulos e placas de vídeo em **bytes**.

**Organização**: `capacidade_por_pino × nº_pinos_DQ`  
Exemplo chip 8 Gibit:
- `2 Gibit × 4` → 4 pinos DQ
- `1 Gibit × 8` → 8 pinos DQ
- `512 Mibit × 16` → 16 pinos DQ

**Capacidade do módulo** = (capacidade do chip em bytes) × (nº de chips por lado) × (nº de lados)  
Ex: 8 chips × 1 GiB × 1 lado = **8 GiB**

**Placa de vídeo**: barramento GPU dividido em canais de 32 bits; chips GDDR de 32 bits.  
Ex: GPU 256 bits → 8 chips GDDR de 32 bits.

---

## Largura de Banda

$$\text{Largura de banda} = \frac{\text{clock efetivo} \times \text{largura do canal} \times \text{canais}}{8}$$

Resultado em bytes/s.

| Configuração | Largura de banda |
|---|---|
| DDR4-3200, 1 canal | 25,6 GB/s |
| DDR4-3200, 2 canais | 51,2 GB/s |
| DDR4-2133, 2 canais | 34,1 GB/s |

> DDR4-2133 dual channel (34 GB/s) > DDR4-3200 single channel (25,6 GB/s)

---

## Módulos de Memória

| Módulo | Memória | Contatos | Bits |
|---|---|---|---|
| DIMM | SDRAM | 168 | 64 |
| DIMM | DDR1 | 184 | 64 |
| DIMM | DDR2 | 240 | 64 |
| DIMM | DDR3 | 240 | 64 |
| DIMM | DDR4 | 288 | 64 |
| DIMM | DDR5 | 288 | 32 DC |
| SODIMM | DDR4 | 260 | 64 |
| SODIMM | DDR5 | 262 | 32 DC |

**Chanfrado delimitador** impede instalação em soquete errado (DDR2 e DDR3 têm 240 pinos mas são incompatíveis).

### Tipos de módulo DIMM

| Tipo | Chip buffer CMD/END | Chip buffer dados | Uso |
|---|---|---|---|
| UDIMM | Não | Não | Consumidor |
| RDIMM | RCD (sim) | Não | Servidor |
| LRDIMM | RCD (sim) | DB (sim) | Servidor (maior capacidade) |

### ECC

- Barramento 72 bits (DDR1–4: 64 + 8 ECC) ou 80 bits (DDR5: 2 × 32 + 2 × 8 ECC)
- Algoritmo **SECDED**: corrige 1 bit, detecta 2 bits
- Funciona em escrita (transmissão), armazenamento e leitura
- DDR5 com ECC: **3 camadas** — chip interno (SEC) + módulo transmissão + módulo armazenamento

### Chip SPD (Serial Presence Detect)

EEPROM acessada via **I2C/SMBus** (não via barramento de dados).  
Armazena: clocks suportados, temporizações, fabricante, modelo.  
- **XMP** (Intel, 2007): perfis de overclock automático
- **EXPO** (AMD, 2022): equivalente XMP para plataformas AMD

### Canais de Memória

- **Dual channel**: 2 módulos simultâneos → ×2 largura de banda
- **Quad channel**: 4 módulos → ×4
- Servidores: 6 ou 8 canais

> Instalar 2 × 4 GiB (dual channel) > 1 × 8 GiB (single channel) para mesma memória total

---

## Conexões com Outras Notas

- [[Memória]] — conceito base (RWM, volátil, acesso aleatório)
- [[Cache]] — SRAM em L1/L2/L3; gap DRAM vs SRAM justifica cache
- [[Hierarquia de Memória]] — DRAM = L4
- [[Microarquitetura Intel Família 6 Parte 3]] — Golden Cove usa HBM2E; Nehalem integrou controlador de memória
- [[Processadores AMD Família Hammer]] — K8 integrou controlador de memória (2003, antes do Nehalem 2008)
- [[Processadores AMD Família Zen]] — EPYC com HBM; chiplet I/O em 12nm (DRAM-like process)
- [[Processadores Intel Híbridos]] — Lakefield com LPDDR4X on-package
- [[Processadores AMD Família Bobcat]] — PS4/Xbox One usam Jaguar + GDDR5
- [[Organização de Chips de Memória]] — pinos CS/RD/OE, combinação de chips, decodificador de endereços