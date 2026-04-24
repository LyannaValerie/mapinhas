---
title: Portas de Comunicação
aliases:
  - SATA
  - SAS
  - USB
  - Thunderbolt
  - USB PD
  - Power Delivery
  - porta de rede
  - Ethernet
  - PS/2
  - SPDIF
  - porta serial
  - eSATA
  - SATA Express
  - U.2
  - U.3
tags:
  - computação/fundamentos
  - computação/hardware
date: 2026-04-12
source: "Livro Hardware clubedohardware.com.br — Cap. 39"
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Hardware]]

# Portas de Comunicação

Conectores da [[Placa-mãe]] para ligação com dispositivos internos e externos.

> [!note] Terminologia: porta ≠ entrada
> Portas são **bidirecionais** (entrada + saída). Chamar de "entrada SATA" ou "entrada USB" é incorreção técnica. Dúvida: usar "conector" — sempre correto.

## Relações (SPO)

- SATA → é → conexão ponto a ponto em série, half-duplex
- SAS → sucede → SCSI paralelo; voltado a servidores
- USB → pode ser → ponto a ponto (único dispositivo) ou barramento (via hub)
- USB4 → tunel → PCIe e DisplayPort sobre conexão USB
- Thunderbolt → multiprotocolo → PCIe + DisplayPort + USB pelo mesmo conector
- Ethernet → controlada por → PHY (chipset/processador) ou controlador dedicado via PCIe
- PS/2 → controlada por → Super I/O (ver [[Placa-mãe]])
- SPDIF → transmite → áudio digital (coaxial RCA ou óptico Toslink)

---

## Portas SATA

Conexão de armazenamento: HDs, SSDs, unidades ópticas. **Ponto a ponto** — um dispositivo por porta.

| Padrão | Codificação | Largura de banda (bit/s) | Largura de banda (byte/s) |
|---|---|---|---|
| SATA-150 (SATA 1) | 8b/10b | 1,5 Gbit/s | 150 MB/s |
| SATA-300 (SATA 2) | 8b/10b | 3 Gbit/s | 300 MB/s |
| SATA-600 (SATA 3) | 8b/10b | 6 Gbit/s | 600 MB/s |

> Codificação 8b/10b → largura de banda em byte/s = Gbit/s ÷ 10 (não ÷ 8).  
> Comunicação **half-duplex**. Portas fisicamente idênticas entre versões — verificar manual.  
> SATA-150 e SATA-300 obsoletos; SATA-600 é o padrão atual.  
> SSDs de alto desempenho saturem SATA-600 → usam PCIe via slot ou M.2.

### Variantes Obsoletas

| Variante | Descrição |
|---|---|
| **eSATA** | SATA externo; substituído pelo USB 3.x com largura de banda equivalente |
| **SATA Express** | Combinava 2× SATA-600 + 2 pistas PCIe; conector triplo; abandonado em favor do M.2 |

---

## Portas SAS

Equivalente SATA para **servidores** (SCSI em série). Full-duplex (raro uso na prática). Suporta **hotswap**.  
Compatível com SATA (SAS aceita SATA; SATA não aceita SAS — SATA só entende ATA, SAS entende SCSI e ATA).  
Até 65.536 dispositivos por porta via expansores. Controlador: chip separado ou placa de expansão via PCIe.

| Padrão | Codificação | Largura de banda (bit/s) | Largura de banda (byte/s) |
|---|---|---|---|
| SAS-1 | 8b/10b | 3 Gbit/s | 300 MB/s |
| SAS-2 | 8b/10b | 6 Gbit/s | 600 MB/s |
| SAS-3 | 8b/10b | 12 Gbit/s | 1.200 MB/s |
| SAS-4 | 128b/150b | 24 Gbit/s | 2.400 MB/s |

### Conectores SAS — Backplane (Tabela 39.3)

Instalação de unidades de armazenamento em gavetas sem parafusos. Todos **mecanicamente compatíveis** entre si; retrocompatibilidade elétrica não garantida (novo em antigo).

| Conector | Tipo | Pistas | Padrão |
|---|---|---|---|
| SFF-8482 | Padrão | 2 | SAS-1 |
| SFF-8678 | Padrão | 2 | SAS-2 |
| SFF-8680 | Padrão | 2 | SAS-3 |
| SFF-8681 | Padrão | 2 | SAS-4 |
| SFF-8630 | MultiLink | 4 | SAS-3 |
| SFF-8640 | MultiLink | 4 | SAS-4 |
| SFF-8639 | Multifunção | 6 | SAS-3 |
| SFF-8638 | Multifunção | 6 | SAS-4 |

Pistas podem ser combinadas (como PCIe):
- SFF-8680: 2× SAS-3 → 24 Gbit/s combinado
- SFF-8681: 2× SAS-4 → 48 Gbit/s combinado
- SFF-8630 MultiLink (4 pistas): 2×24 Gbit/s ou 1×48 Gbit/s (SAS-3)

#### Conectores Multifunção — U.2 e U.3

SFF-8639/8638: 4 pistas PCIe 3.0 + 2 portas SATA/SAS = **U.2**.  
**U.3** (modo triplo): controlador único para SAS + SATA + PCIe; 4 pistas compartilhadas; suporta SCSI Express.

### Conectores SAS — Uso Interno

| Conector | Tipo | Pistas | Padrão |
|---|---|---|---|
| SFF-8484 | Multilane | 2 ou 4 | SAS-1 |
| SFF-8087 | Mini SAS | 4 | SAS-2 |
| SFF-8643 | Mini SAS HD | 4 ou 8 | SAS-3 |
| SFF-8673 | Mini SAS HD | 4 ou 8 | SAS-4 |
| SFF-8654 | Slim SAS | 4 ou 8 | SAS-4 |
| SFF-8621 | MiniLink (plugue SFF-8611 / receptáculo SFF-8612) | 4 ou 8 | SAS-4 |

SFF-8643 e SFF-8673 (Mini SAS HD) mecanicamente compatíveis (norma SFF-8613). Demais: incompatíveis entre si.

### Conectores SAS — Uso Externo

Cabo passivo cobre: ≤6 m (SFF-8088: ≤10 m). Cabo ativo cobre: ≤20 m. Cabo ativo óptico: ≤100 m.

| Conector | Tipo | Pistas | Cabos | Padrão |
|---|---|---|---|---|
| SFF-8470 | Multilane | 4 | PC | SAS-1 |
| SFF-8088 | Mini SAS | 4 | PC, AC | SAS-2 |
| SFF-8644 | Mini SAS HD | 4 ou 8 | PC, AC, AO | SAS-3 |
| SFF-8674 | Mini SAS HD | 4 ou 8 | PC, AC, AO | SAS-4 |
| SFF-8685 | QSFP14 | 4 | PC, AC, AO | SAS-3 |
| SFF-8665 | QSFP28 | 4 | PC, AC, AO | SAS-4 |

SFF-8644 e SFF-8674 (norma SFF-8614): mecanicamente compatíveis. QSFP: mecanicamente compatíveis entre si.

### Variantes SCSI

| Variante | Descrição |
|---|---|
| **NL-SAS** | Discos SATA com interface SAS; desempenho inferior ao SAS nativo |
| **iSCSI** | SCSI sobre TCP/IP |
| **SCSI Express (SCSIe)** | SAS via PCIe |
| **SCSI paralelo** | Obsoleto |

---

## USB

Conexão universal para periféricos externos. Também em conectores **internos** na placa-mãe (painel frontal, periféricos internos).

| Padrão | Nome original | Codificação | Transmissão | Largura de banda (bit/s) | Largura de banda (byte/s) |
|---|---|---|---|---|---|
| USB 1.1 | 1.1 Low-Speed | NRZ | Half-duplex | 1,5 Mbit/s | 187,5 kB/s |
| USB 1.1 | 1.1 Full-Speed | NRZ | Half-duplex | 12 Mbit/s | 1,5 MB/s |
| USB 2.0 | 2.0 Hi-Speed | NRZ | Half-duplex | 480 Mbit/s | 60 MB/s |
| USB 3.2 Gen 1x1 | 3.0 SuperSpeed | 8b/10b | Full-duplex | 5 Gbit/s | 500 MB/s |
| USB 3.2 Gen 2x1 | 3.1 SuperSpeed+ 10 Gbps | 128b/132b | Full-duplex | 10 Gbit/s | 1,2 GB/s |
| USB 3.2 Gen 2x2 | 3.2 SuperSpeed+ 20 Gbps | 128b/132b | Full-duplex | 20 Gbit/s | 2,4 GB/s |
| USB4 Gen 2x2 | — USB 20 Gbps | 64b/66b | Full-duplex | 20 Gbit/s | 2,4 GB/s |
| USB4 Gen 3x2 | — USB 40 Gbps | 128b/132b | Full-duplex | 40 Gbit/s | 4,8 GB/s |

> Valores USB 3.2 Gen 2x1+ arredondados. Padrões Gen 1x2, Gen 2x1*, USB4 Gen 2x1 e Gen 3x1 existem na especificação mas não são utilizados na prática.

### Nomenclatura (confusa)

| Nome original | Revisão 2013 | Revisão 2017 | Apelido |
|---|---|---|---|
| USB 3.0 | USB 3.1 Gen 1 | USB 3.2 Gen 1x1 | USB 3.2 Gen 1 |
| USB 3.1 | USB 3.1 Gen 2 | USB 3.2 Gen 2x1 | USB 3.2 Gen 2 |
| USB 3.2 | — | USB 3.2 Gen 2x2 | — |

"USB4" grafado sem espaço. "USB 3.2" sozinho é ambíguo.

### Desempenho

- USB com hub = barramento compartilhado; todos os dispositivos dividem a largura de banda
- Dispositivo mais lento limita a velocidade
- Para SSDs externos: mínimo USB 3.2 Gen 2x1 (USB 3.1)
- Largura de banda teórica nunca atingida: overhead de controle consome parte do canal

### Conectores USB

| Versão | Cor | Conector | Pinos/contatos |
|---|---|---|---|
| USB 1.1 / 2.0 | Preto | Tipo A, B, Mini A/B, Micro A/B | 4 ou 5 |
| USB 3.2 Gen 1x1 (USB 3.0) | **Azul** | Tipo A, B, Micro B | Mais contatos (+ 2 pares full-duplex + par USB 2.0) |
| USB 3.2 Gen 2x1+ / USB4 | — | **Tipo C** (reversível) | — |

Conectores internos: cada conector interno = 2 portas USB.

### Alimentação USB (USB PD — Power Delivery)

Padrão base por versão:

| Padrão | Modo baixa potência | Modo alta potência |
|---|---|---|
| USB 1.1 / 2.0 | 100 mA / 0,5 W | 500 mA / 2,5 W |
| USB 3.x (1 pista) | 150 mA / 0,75 W | 900 mA / 4,5 W |
| USB 3.x (2 pistas) | 250 mA / 1,25 W | 1,5 A / 7,5 W |
| Tipo C | 1,5 A / 7,5 W | 3 A / 15 W |

Especificação adicional **USB PD** para maior potência:

| Tensão | Corrente | Potência | Modo | Cabo |
|---|---|---|---|---|
| 5 V | 3 A | 15 W | SPR, EPR | 3 A |
| 9 V | 3 A | 27 W | SPR, EPR | 3 A |
| 15 V | 3 A | 45 W | SPR, EPR | 3 A |
| 20 V | 3 A | 60 W | SPR, EPR | 3 A |
| 20 V | 5 A | 100 W | SPR, EPR | 5 A / EPR |
| 28 V | 5 A | 140 W | EPR | EPR |
| 36 V | 5 A | 180 W | EPR | EPR |
| 48 V | 5 A | 240 W | EPR | EPR |

> PD negocia modo máximo entre os dispositivos. Cabo também deve suportar o modo pretendido. USB4 segue limite do Tipo C.

---

## Thunderbolt

Desenvolvido Intel + Apple. Multiprotocolo: PCIe + DisplayPort + USB num único conector.  
Versões 1/2: conector **Mini DisplayPort**. Versões 3/4: conector **USB Tipo C**.

| Versão | Largura de banda | DisplayPort | PCIe | USB | VT-d | Conector |
|---|---|---|---|---|---|---|
| 1 | 10 Gbit/s | 1.1a x4 (1 link) | 2.0 x4 | 2.0 | Não | Mini DisplayPort |
| 2 | 20 Gbit/s | 1.1a x4 (1 link) | 2.0 x4 | 2.0 | Não | Mini DisplayPort |
| 3 | 40 Gbit/s | 1.2 (1 link HBR2 x4, 2 links HBR2 ou 1 link HBR3)* | 3.0 x2 ou x4* | USB 3.2 Gen 2x1 | Não | USB Tipo C |
| 4 | 40 Gbit/s | 1.4 x8 (2 links HBR3) | 3.0 x4 | USB4 | **Sim** | USB Tipo C |

*Thunderbolt 3: capacidades reais dependem do chip controlador (pode usar x2 PCIe, um único monitor 4K).  
Thunderbolt 4: sempre x4 PCIe, 2 links DP → 2× monitor 4K ou 1× monitor 8K.

### Modos de Operação

| Modo | Descrição |
|---|---|
| **USB** | Funciona como porta USB comum; outro lado não precisa ser Thunderbolt |
| **DisplayPort** | Funciona como porta DisplayPort; adaptador necessário (Tipo C → DP) |
| **DisplayPort + USB** | DP (menos pistas) + USB 3.2 Gen 2x1 simultâneos |
| **Thunderbolt** | Outro lado deve ser Thunderbolt; até 4× PCIe + 2× DisplayPort |
| **Rede** | Ethernet virtual TCP/IP entre duas máquinas Thunderbolt |

- Até 6 dispositivos Thunderbolt em cascata por porta
- Um único dispositivo USB ou DP sem Thunderbolt por porta (deve ser o último da cadeia)
- Prioridade ao tráfego de vídeo se largura de banda excedida
- Thunderbolt 4 exige **VT-d** (proteção contra Thunderspy)
- Cabos ópticos disponíveis para distâncias maiores
- Controlado por chip separado na placa-mãe (ligado ao processador/chipset via PCIe)

---

## Porta de Rede (Ethernet)

Conector **8P8C** (incorretamente chamado "RJ-45"). Velocidade não visível externamente.

| Padrão | Cabeamento | Largura de banda |
|---|---|---|
| Ethernet | 10BASE-T | 10 Mbit/s |
| Fast Ethernet | 100BASE-T | 100 Mbit/s |
| Gigabit Ethernet | 1000BASE-T | 1 Gbit/s |
| 2.5G Ethernet | 2.5GBASE-T | 2,5 Gbit/s |
| 5G Ethernet | 5GBASE-T | 5 Gbit/s |
| 10G Ethernet | 10GBASE-T | 10 Gbit/s |

Controle: **PHY** (chip interface física) + chipset/processador (HSP), ou controlador dedicado via PCIe.  
Duas portas de rede → **link aggregation (teaming)** (dobra banda) ou redundância.

---

## Rede Sem Fio / Bluetooth

Placa IEEE 802.11 (Wi-Fi) + Bluetooth, disponível em formatos:
- Slot PCIe
- Slot proprietário
- **M.2** padrão
- **M.2 CNVi** (Intel)

Portáteis: sempre inclusa, normalmente M.2 ou Mini PCIe (mais antigos).

---

## Portas PS/2

Conectores redondos de 5 pinos. Verde = mouse. Lilás = teclado. Controladas pelo **Super I/O** (ver [[Placa-mãe]]).  
Dados em série. 4 pinos usados (dados, GND, +5V, clock). Algumas placas: apenas 1 conector PS/2 (cor dividida lilás+verde).  
Substituídas pelo USB; muitas placas modernas não têm mais.

---

## Conectores de Áudio

Controle por processador via chipset (**HSP**) ou controlador dedicado via PCIe (raro, topo de linha).

### Analógico (3,5 mm / P2)

| Cor | Função |
|---|---|
| Rosa | Mic in (entrada de microfone) |
| Azul | Line in (entrada de linha) |
| Verde | Alto-falantes frontais |
| Preta | Alto-falantes traseiros |
| Laranja | Alto-falante central + subwoofer |
| Cinza | Surround central |

Configurações: 2 canais, 6 canais (saídas independentes ou compartilhadas), 8 canais.  
Saídas compartilhadas: mic in e line in assumem funções de conectores ausentes.

### Digital (SPDIF)

| Tipo | Conector |
|---|---|
| SPDIF coaxial | RCA |
| SPDIF óptico | Toslink |

Notebooks: conector 3,5 mm com luz vermelha interna = saída SPDIF óptica embutida.  
Pinos SPDIF_OUT na placa-mãe permitem adaptadores mesmo sem conector físico externo.

---

## Porta Serial

Obsoleta (115,2 Kbps máx.). Mantida em algumas placas para equipamentos de automação/controle industrial de alto custo.  
Conector: D-Sub 9 (macho) ou pinos internos "COM1" + adaptador. Adaptadores USB/serial disponíveis.

---

## Ver também

- [[Placa-mãe]] — chipset controla SATA/USB/Ethernet/áudio; Super I/O controla PS/2; diagrama de conexões
- [[Slots de Expansão]] — SSDs NVMe via PCIe/M.2; controladores externos USB/SAS via PCIe x1
- [[Memória Flash]] — SSDs SATA vs. SSDs NVMe (PCIe)
- [[Barramento]] — USB com hub = barramento; SATA e Ethernet = ponto a ponto
- [[Hierarquia de Memória]] — armazenamento (L5) acessado via SATA ou PCIe
- [[Conectores de Vídeo]] — Thunderbolt como conector de vídeo; DisplayPort via USB Tipo C
- [[Áudio Digital]] — SPDIF (coaxial RCA + Toslink), conectores P2, interface MIDI
