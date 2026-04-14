---
title: Firmware
aliases:
  - BIOS
  - UEFI
  - POST
  - Power On Self Test
  - Unified Extensible Firmware Interface
  - Basic Input/Output System
tags:
  - computação/fundamentos
date: 2026-04-09
---

# Firmware

## Definição
Programa gravado permanentemente em uma memória do tipo ROM (somente leitura), tornando-o **não-volátil** — ao contrário de software na [[Memória de Massa]], o firmware já está disponível ao ligar o computador, sem precisar ser carregado.

> [!note] Terminologia invariável
> Assim como "software" e "hardware", a palavra "firmware" é **invariável** em inglês — "firmwares" não existe. São substantivos incontáveis.

## Relações (SPO)
- Firmware → armazenado em → ROM (não-volátil)
- Firmware → executado por → [[Processador]] ao ligar
- Firmware → contém → UEFI (modernos) ou BIOS (antigos)
- Firmware → inicia → POST
- POST → precede → boot do [[Sistema Operacional]]
- UEFI → sucede → BIOS

## UEFI e BIOS

| | BIOS | UEFI |
|---|---|---|
| Nome completo | Basic Input/Output System | Unified Extensible Firmware Interface |
| Uso | PCs antigos (até ~2020) | PCs modernos (a partir de 2007–2008) |
| Armazenado em | Chip ROM na [[Placa-mãe]] | Chip ROM (flash) na [[Placa-mãe]] |
| Modo de operação | Modo real (limite 1 MiB RAM) | Modo protegido/64 bits |
| Interface | Texto | Gráfica |
| Carregamento SO | MBR (bootstrap; limite 2 TiB) | GPT (64 bits; limite 8 ZiB) |
| Secure Boot | Não | Sim |
| Apps na ROM | Não | Sim |

Ambos são firmware: programas gravados na ROM que inicializam o hardware e transferem o controle para o sistema operacional.

> Intel declarou em 2017 que não fabricaria chips compatíveis com BIOS a partir de **2020**. "BIOS" virou sinônimo informal de qualquer firmware/ROM de placa-mãe.

### BIOS — Limitações

- Executado em **modo real** → acessa apenas **1 MiB** de RAM
- Carregamento do SO via **bootstrap** lido do **MBR** (Master Boot Record, 1º setor da unidade)
  - MBR usa endereçamento 32 bits × setores de 512 bytes = limite **2 TiB**
  - Não é limite do BIOS em si, mas do MBR — não há "remendo" possível
- Compatibilidade forçada com software antigo acumula remendos ao longo do tempo
- POST lento, telas desnecessárias para usuário comum

### POST — rotinas principais

1. Identifica configuração instalada
2. Inicializa chipset da placa-mãe
3. Inicializa vídeo
4. Testa memória
5. Testa teclado
6. Carrega SO → RAM
7. Entrega controle ao SO

> A ROM física contém **três programas distintos**: BIOS (firmware), POST (autoteste) e BIOS setup (configuração). "Entrar na BIOS" = entrar no **BIOS setup**, não no BIOS propriamente.

### UEFI — Destaques

- **GPT** (GUID Partition Table): endereçamento 64 bits → suporta unidades até **8 ZiB** (2⁶⁴ × 512 B)
- **Secure Boot**: verifica autenticidade de todos os componentes durante boot
- **Modo gráfico**: acessa > 1 MiB de RAM; interface visual
- **Apps na ROM**: utilitários executáveis sem carregar SO (ex: atualizador de firmware)
- Boot mais rápido, POST silencioso
- Windows suporta UEFI a partir do Vista SP1 / Windows 7
- MBR ainda suportado em modo de compatibilidade ("legacy BIOS")
- Partições GPT preferíveis mesmo em discos < 2 TiB (desempenho e modo nativo)

## Sequência de inicialização

```
1. Computador ligado → RAM vazia, processador não sabe o que fazer
2. Processador busca instruções na ROM (endereço fixo de reset)
3. Firmware (UEFI/BIOS) inicia
4. POST é executado — autoteste dos componentes
5. Firmware instrui carregamento do SO do armazenamento → RAM (boot)
6. Processador passa a executar o SO
```

### POST — Power On Self Test
Autoteste executado pelo firmware imediatamente ao ligar o computador. Verifica os componentes de hardware antes de transferir o controle ao sistema operacional.

### Boot
Processo de carregamento do [[Sistema Operacional]] do [[Memória de Massa|armazenamento de massa]] para a [[Memória|RAM]]. Durante o boot:
- O processador já executa instruções da RAM à medida que mais chegam do armazenamento
- A transferência usa [[DMA]] (acesso direto à memória), sem envolver o processador diretamente

> [!tip] Por que "boot"?
> Abreviação de "bootstrapping" — puxar-se pelas próprias botas. O computador se auto-inicializa: usa um programa pequeno (firmware na ROM) para carregar um programa maior (SO na RAM).

## Memória de Configuração (CMOS RAM)

Armazena as configurações do BIOS setup (clocks suportados, ordem de boot, etc.).

- Tipo **RAM** (volátil) → precisa de alimentação contínua
- Mantida por uma **bateria na placa-mãe** (alimenta também o RTC)
- **RTC** (Real-Time Clock): circuito de relógio em tempo real, alimentado pela mesma bateria
- Localização atual: embutida no **chipset** (ponte sul / PCH / ICH)
- Apelido histórico "**CMOS**": quando surgiu, era a única memória com tecnologia CMOS no sistema; o nome persistiu mesmo sem chip CMOS dedicado

> "Memória CMOS" = memória de configuração. Tecnicamente é RAM, não ROM.

## Ver também
- [[ROM — Tipos]] — Mask-ROM, PROM, EPROM, EEPROM, Flash-ROM
- [[Memória]] — diferença entre RAM (volátil) e ROM (não-volátil)
- [[RAM — Tecnologias e Módulos]] — chip SPD do módulo = EEPROM (mesmo princípio de configuração)
- [[Modos de Operação x86]] — modo real (limitação do BIOS)
- [[Processador]] — ciclo de execução que começa pelo firmware ao ligar
- [[Sistema Operacional]]
- [[DMA]] — transferência sem CPU durante o boot
- [[Placa-mãe]] — local físico do chip ROM com firmware
