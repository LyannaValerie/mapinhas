---
title: BIOS INT 10h
aliases:
  - interrupção de vídeo BIOS
  - BIOS video interrupt
  - serviços de vídeo BIOS
tags:
  - computação/arquitetura
date: 2026-04-24
source: "PC System Programming — Abacus, cap. 7"
codex_note: criado pelo Codex
---

# BIOS INT 10h

## Definição

`INT 10h` é o serviço de [[BIOS do IBM PC]] para saída de vídeo, controle de modo gráfico/texto, cursor, páginas de vídeo, rolagem e escrita de caracteres.

## Funções

| AH | Função |
|---|---|
| 00h | Set video mode |
| 01h | Define cursor appearance (CH=start line, CL=end line) |
| 02h | Position cursor (BH=page, DH=row, DL=col) |
| 03h | Read cursor position → DH=row, DL=col, CH=start, CL=end |
| 04h | Read lightpen position |
| 05h | Select display page (AL=page) |
| 06h | Scroll window up (AL=lines, BH=attribute, CH/CL=top-left, DH/DL=bottom-right) |
| 07h | Scroll window down |
| 08h | Read character/attribute at cursor → AH=attr, AL=char |
| 09h | Write character+attribute (BH=page, BL=attr, AL=char, CX=count) |
| 0Ah | Write character only (sem mudar atributo) |
| 0Bh | Select palette (sub-fn 0: border color; sub-fn 1: palette) |
| 0Ch | Write graphic pixel |
| 0Dh | Read graphic pixel |
| 0Eh | Write character (TTY mode — avança cursor) |
| 0Fh | Read display mode → AH=cols, AL=mode, BH=page |
| 12h | EGA/VGA extended functions (sub-fns via BL) |
| 13h | Write character string (AT only) — AL=mode, BH=page, BL=attr |

## Modos de vídeo (AH=00h, AL=modo)

| AL | Resolução | Cores | Tipo |
|---|---|---|---|
| 00h–01h | 40×25 | 16 | texto colorido |
| 02h–03h | 80×25 | 16 | texto colorido |
| 04h–05h | 320×200 | 4 | gráfico |
| 06h | 640×200 | 2 | gráfico |
| 07h | 80×25 | mono | texto monocromático |
| 0Dh | 320×200 | 4 | gráfico EGA |
| 0Eh | 640×200 | 16 | gráfico EGA |
| 0Fh | 640×350 | mono | gráfico EGA |
| 10h | 640×350 | 16 | gráfico EGA/VGA |
| 11h | 640×480 | 2 | gráfico VGA |
| 12h | 640×480 | 16 | gráfico VGA |
| 13h | 320×200 | 256 | gráfico VGA |

> [!note] Bit 7 de AL
> Se bit 7 = 1 ao chamar fn 00h, a RAM de vídeo **não é apagada** ao trocar de modo.

## Sub-funções EGA/VGA (AH=12h)

| BL | Sub-função |
|---|---|
| 30h | Selecionar número de scan lines (AL=0→200, 1→350, 2→400) |
| 31h | Toggle carregamento automático de paleta |
| 32h | Enable/disable acesso CPU à RAM de vídeo |
| 33h | Enable/disable gray scaling automático nos registradores DAC |
| 34h | Enable/disable emulação de cursor de texto |
| 36h | Suprimir/restaurar refresh de tela |

## Atributos (modo texto)

Em modo texto cada célula é um par `ASCII/atributo`. O atributo codifica:
- bits 0–2: cor de frente
- bit 3: intensidade
- bits 4–6: cor de fundo
- bit 7: piscar ou intensidade de fundo (depende do modo)

## Relações (SPO)

- [[BIOS INT 10h]] pertence a [[Serviços de BIOS do IBM PC]].
- [[BIOS INT 10h]] abstrai adaptadores como MDA, CGA, EGA, VGA e Hercules.
- [[BIOS INT 10h]] depende de convenções de registradores x86.

## Ver também

- [[BIOS do IBM PC]]
- [[Serviços de BIOS do IBM PC]]
- [[Monitores de Vídeo]]
- [[Interrupção]]
