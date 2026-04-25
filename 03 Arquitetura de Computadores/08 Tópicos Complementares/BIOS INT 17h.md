---
title: BIOS INT 17h
aliases:
  - interrupção de impressora BIOS
  - BIOS printer interrupt
  - serviços de impressora BIOS
tags:
  - computação/arquitetura
date: 2026-04-24
source: "PC System Programming — Abacus, cap. 7"
codex_note: criado pelo Codex
---

# BIOS INT 17h

## Definição

`INT 17h` é o serviço de [[BIOS do IBM PC]] para comunicação básica com impressoras paralelas.

## Funções

| AH | Função | Entrada | Saída |
|---|---|---|---|
| 00h | Escrever caractere | AL=char, DX=porta (0=LPT1, 1=LPT2) | AH=status |
| 01h | Inicializar impressora | DX=porta | AH=status |
| 02h | Ler status da impressora | DX=porta | AH=status |

## Byte de status (AH retornado)

| Bit | Significado |
|---|---|
| 3 | I/O error |
| 4 | Impressora selecionada |
| 5 | Out of paper |
| 6 | Acknowledgement |
| 7 | Not busy (impressora disponível) |

> [!note] Porta paralela
> DX=0 → LPT1, DX=1 → LPT2, DX=2 → LPT3. Endereços físicos das portas ficam nas variáveis BIOS em `0040:0008`.

## Uso típico

```asm
mov ah, 00h     ; escrever caractere
mov al, 'A'     ; caractere a imprimir
mov dx, 0       ; LPT1
int 17h
; AH = byte de status após operação
test ah, 08h    ; bit 3 = I/O error?
jnz erro
```

## Relações (SPO)

- [[BIOS INT 17h]] pertence a [[Serviços de BIOS do IBM PC]].
- [[BIOS INT 17h]] abstrai porta paralela para software legado.
- [[BIOS INT 17h]] interage com variáveis BIOS relacionadas a hardcopy.

## Ver também

- [[BIOS do IBM PC]]
- [[Impressoras]]
- [[Serviços de BIOS do IBM PC]]
- [[Variáveis BIOS]]
