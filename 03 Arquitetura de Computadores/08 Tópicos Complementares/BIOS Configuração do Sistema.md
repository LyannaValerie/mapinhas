---
title: BIOS Configuração do Sistema
aliases:
  - configuração BIOS do IBM PC
  - equipamento instalado BIOS
  - BIOS system configuration
tags:
  - computação/arquitetura
date: 2026-04-24
source: "PC System Programming — Abacus, cap. 7"
codex_note: criado pelo Codex
---

# BIOS Configuração do Sistema

## Definição

BIOS Configuração do Sistema é o conjunto de rotinas e variáveis usadas para identificar tipo de máquina, periféricos instalados, memória disponível e recursos básicos do PC.

## INT 11h — Determinar configuração (equipment list)

Retorna palavra de equipamento em `AX`.

```asm
int 11h    ; AX = palavra de configuração
```

### Bits da palavra de configuração (PC/XT)

| Bits | Significado |
|---|---|
| 0 | 1 = drive de disco presente |
| 1 | 1 = coprocessador matemático presente |
| 2–3 | RAM na placa principal (em blocos de 16K; depende do modelo) |
| 4–5 | Modo de vídeo no boot: `01`=40×25 color, `02`=80×25 color, `03`=80×25 mono |
| 6–7 | Número de drives (se bit 0=1): `00`=1, `01`=2, `10`=3, `11`=4 |
| 8 | Não utilizado |
| 9–11 | Número de portas RS-232 |
| 12–13 | Não utilizado |
| 14–15 | Número de impressoras |

### Diferença AT

No AT os bits têm interpretação parcialmente diferente; bit 0 indica presença de DMA chip em vez de drive. O modo de vídeo no boot (bits 4–5) representa o modo ativo na inicialização — para o modo atual usar INT 10h, fn 0Fh.

## INT 12h — Determinar tamanho da memória

```asm
int 12h    ; AX = memória convencional em KB
```

- PC/XT: máximo 640 KB
- AT: retorna apenas memória convencional (≤640 KB); para memória estendida usar INT 15h, fn 88h

## Uso

Programas legados consultam INT 11h para adaptar comportamento ao hardware sem inspecionar portas fisicamente. O modo de vídeo registrado em bits 4–5 identifica o adaptador ativo no boot.

## Relações (SPO)

- [[BIOS Configuração do Sistema]] complementa [[Serviços de BIOS do IBM PC]].
- [[BIOS Configuração do Sistema]] usa [[Variáveis BIOS]].
- [[PC]] expõe compatibilidade por convenções de firmware e hardware.

## Ver também

- [[BIOS do IBM PC]]
- [[Variáveis BIOS]]
- [[Firmware]]
- [[PC]]
