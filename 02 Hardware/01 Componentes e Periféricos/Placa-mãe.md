---
title: Placa-mãe
aliases:
  - motherboard
  - mainboard
  - placa principal
  - ATX
  - microATX
  - Mini-ITX
tags:
  - computação/fundamentos
  - computação/hardware
date: 2026-04-12
source: "Livro Hardware clubedohardware.com.br — Cap. 37"
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Hardware]]

# Placa-mãe

## Definição
Maior placa de circuito impresso do computador. Serve como **apoio físico** e realiza a **conexão elétrica** entre todos os componentes.

## Relações (SPO)

- Placa-mãe → conecta eletricamente → todos os componentes do [[PC]]
- Placa-mãe → hospeda → [[Processador]], [[Memória]], Chipset
- Chipset → conecta-se ao processador via → DMI (Intel) / UMI (AMD) / PCIe
- SoC → integra → funções do chipset no próprio processador (sem chipset externo)
- Super I/O → conecta-se ao chipset via → LPC
- ROM e TPM → conectam-se ao chipset via → SPI
- Bateria CR2032 → alimenta → memória de configuração (CMOS RAM) + RTC

---

## Componentes da Placa-mãe

| Componente | Detalhes |
|---|---|
| **Soquete do processador** | PGA (AMD: pinos no processador) ou LGA (Intel: pinos no soquete); pode ser ausente (processador soldado) |
| **Soquetes de memória** | Definem tipo (DDR4/DDR5) e capacidade máxima |
| **Chipset** | 1 chip atualmente; ausente em SoC |
| **Super I/O** | PS/2, serial/paralela, disquete, infravermelho, ventoinhas, sensores de temperatura |
| **Slots de expansão** | PCI Express x1, x4, x16 |
| **Portas de comunicação** | SATA, USB, M.2 |
| **Conectores de alimentação** | Principal (24 ou 10 pinos) + processador (4 ou 8 pinos) |
| **Circuito regulador de tensão** | Converte +12 V da fonte nas tensões do processador e demais chips |
| **Chip ROM (BIOS)** | Flash-ROM NOR com UEFI; alguns modelos têm chip de backup |
| **Bateria CR2032** | Lítio; alimenta CMOS RAM e RTC; dura anos |
| **Conectores de ventoinhas** | 3 pinos (monitor RPM) ou 4 pinos (PWM controle de velocidade) |
| **Conectores RGB** | Analógico 4 pinos (cor única) ou digital/endereçável 3 pinos (por LED) |
| **Conectores de vídeo** | Ativos apenas com CPU com iGPU integrado |
| **Conector painel frontal** | LEDs e botões do gabinete |
| **TPM** | Chip ou conector para criptografia e armazenamento de chaves |
| **Mostrador de diagnósticos** | Código 2 dígitos do POST; indica componente com falha |
| **Botões** | Liga/desliga, reset, clear CMOS, clock base (modelos topo) |

---

## Arquitetura — Diagrama de Conexões

```
                    PROCESSADOR
                    ├── Barramento de memória → módulos RAM
                    ├── PCIe (direto) → slots x16 (placa de vídeo)
                    ├── iGPU → conectores de vídeo na placa-mãe
                    ├── [AMD Zen] PCIe/SATA/USB → M.2, discos, USB
                    └── DMI (Intel) / UMI (AMD) / PCIe
                              ↓
                           CHIPSET
                    ├── PCIe → slots x1, x4, x16 (velocidade x4), M.2
                    ├── SATA → discos (Intel/AMD não-Zen)
                    ├── USB → portas USB
                    ├── Codec → conectores de áudio
                    ├── PHY → porta de rede Ethernet
                    ├── CNVi (Intel) → placa Wi-Fi/Bluetooth M.2
                    ├── LPC → Super I/O → PS/2, serial, ventoinhas
                    └── SPI → ROM (UEFI) + TPM
```

> Conexões diretas ao processador = maior desempenho (sem gargalo do chipset).  
> Largura de banda chipset↔processador é **compartilhada** entre todos os periféricos no chipset.

### Diferença AMD Zen vs Intel

| Recurso | AMD Zen | Intel |
|---|---|---|
| USB (alguns modelos) | No processador | No chipset |
| Armazenamento | No processador | No chipset |
| Áudio, rede, Super I/O | No processador (Zen) | No chipset |
| Wi-Fi (CNVi) | Não | Sim (Intel) |

---

## Soquete do Processador

### Tipos de pinagem

| Padrão | Pinos em | Orifícios em | Fabricante |
|---|---|---|---|
| **PGA** (Pin Grid Array) | Processador | Soquete | AMD (tradicional) |
| **LGA** (Land Grid Array) | Soquete | Processador | Intel |

### 3 compatibilidades obrigatórias

1. **Mecânica** — processador encaixa fisicamente no soquete
2. **Elétrica** — sinais do soquete compatíveis com o processador (ex: LGA1151 = 6ª/7ª gen ≠ 8ª/9ª gen, mesmo encaixando)
3. **Lógica** — firmware da placa-mãe suporta o processador (pode exigir atualização de BIOS)

> Verificar lista de processadores compatíveis no site do fabricante da placa-mãe, incluindo versão mínima de firmware.

---

## Formatos (Form Factor)

| Padrão | Largura | Profundidade | Uso típico |
|---|---|---|---|
| **E-ATX / SSI EEB** | 30,5 cm | 33 cm | Servidores, entusiastas |
| **ATX** | 30,5 cm | 24,4 cm | Desktops padrão |
| **microATX** | 24,4 cm | ≤ 24,4 cm | Desktops compactos |
| **Mini-ITX** | 17 cm | 17 cm | HTPCs, mini PCs |
| **Thin Mini-ITX** | 17 cm | 17 cm | Ultra-slim (SODIMM, painel traseiro 25 mm) |
| **Nano-ITX** | 12 cm | 12 cm | Embutidos |
| **Pico-ITX** | 10 cm | 7,2 cm | Embutidos ultra-compactos |

> "Form factor" = formato, layout ou tamanho. "Fator de forma" é tradução equivocada.

---

## Conectores de Alimentação

### Principal

| Padrão | Pinos | Tensões |
|---|---|---|
| ATX12V 2.x / EPS12V / ATX 3.0 | **24 pinos** (2×12) | +3,3 V, +5 V, +12 V, etc. |
| ATX12VO (2020+) | **10 pinos** (2×5) + aux 6 pinos opcional | **Só +12 V** (placa converte para demais tensões) |

### Processador

| Nome | Pinos | Uso |
|---|---|---|
| ATX12V | 4 pinos | Processadores de menor TDP |
| EPS12V | **8 pinos** | Padrão atual; alguns modelos topo têm 2× para overclock |

### SATA (ATX12VO)
Cabos SATA saem da **placa-mãe** (não da fonte), via conectores de 4 pinos na placa que geram +5 V a partir dos +12 V recebidos.

---

## Bateria

- Modelo: **CR2032** (lítio)
- Alimenta: [[Firmware|memória de configuração (CMOS RAM)]] + RTC (relógio de tempo real)
- Falha indicada por: mensagem "CMOS checksum error – Defaults loaded" no POST
- Substituição: fácil, encontrada em lojas de informática e relojoarias

---

## Conectores de Ventoinhas

| Tipo | Pinos | Capacidade |
|---|---|---|
| 3 fios | Preto (GND), vermelho (+12 V), amarelo/verde (sensor RPM) | Monitora velocidade; controle limitado |
| 4 fios (PWM) | + azul (controle PWM) | Controla velocidade de rotação |

---

## Iluminação RGB

| Tipo | Pinos | Controle |
|---|---|---|
| Analógico | 4 pinos | Cor única para todos os LEDs |
| Digital / endereçável | 3 pinos (1 removido) | Cor individual por LED |

LEDs: fitas 3528 (cor fixa) ou 5050 (RGB configurável).

---

## Mostrador de Diagnósticos (POST Codes)

Código de 2 dígitos indica teste atual do POST.  
Se o computador não ligar: código = último teste executado antes da falha → identifica componente com problema.  
Ver [[Firmware]] para descrição das rotinas do POST.

---

## Camadas de Cobre

| Espessura | Peso | Resistência | Calor |
|---|---|---|---|
| 35 µm (padrão) | 1 oz/ft² | Normal | Normal |
| 70 µm (premium) | 2 oz/ft² | Menor | Menor |

Trilhas mais espessas nas camadas internas de alimentação. Fabricantes anunciam como "2 oz".

---

## Ver também

- [[Slots de Expansão]] — PCIe versões/pistas/slots; M.2 configurações B/M; Mini PCIe; mSATA
- [[Portas de Comunicação]] — SATA/SAS/USB/Thunderbolt/Ethernet/áudio/PS/2
- [[Circuito Regulador de Tensão]] — VRM/VID/fases/PWM/DrMOS/componentes
- [[Firmware]] — UEFI/BIOS/POST/CMOS RAM/bateria/TPM
- [[ROM — Tipos]] — chip Flash-ROM NOR na placa-mãe
- [[RAM — Tecnologias e Módulos]] — soquetes DIMM, compatibilidade DDR
- [[Processador]] — soquetes, controladores integrados (memória, PCIe, vídeo)
- [[Memória Virtual]] — CMOS RAM (memória de configuração) ≠ RAM principal
- [[PC]]
- [[Fonte de Alimentação]] — conectores de alimentação; padrões ATX/EPS12V/ATX12VO; +5VSB; PWR_OK
