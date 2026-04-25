---
title: MS-DOS
aliases:
  - DOS
  - Disk Operating System
  - sistema operacional DOS
tags:
  - computação/sistemas
date: 2026-04-24
created_by: Codex
source: desconhecida
---

# MS-DOS

Nota criada pelo Codex.

## Definição

MS-DOS é um sistema operacional para PCs compatíveis com IBM PC, organizado em torno de programas carregados em memória, chamadas por interrupção e dispositivos representados por nomes convencionais.

O serviço central para programas é a interrupção `INT 21h`, usada para entrada e saída, execução de programas, alocação de memória, terminação e acesso a arquivos.

## Relações (SPO)
- MS-DOS → usa → `INT 21h`
- Programa DOS → recebe → [[Program Segment Prefix]]
- Programa DOS → pode ter formato → [[Programa COM]]
- Programa DOS → pode ter formato → [[Programa EXE]]
- `EXEC` → carrega → programa filho
- MS-DOS → gerencia → [[Alocação de Memória DOS]]
- MS-DOS → suporta → [[Driver de Dispositivo DOS]]
- MS-DOS → permite → [[Programa TSR DOS]]

---

## Dispositivos Padrão

DOS expõe dispositivos por handles e nomes convencionais:

| Handle | Dispositivo padrão |
|---|---|
| `0` | entrada padrão, normalmente `CON` |
| `1` | saída padrão, normalmente `CON` |
| `2` | saída de erro, normalmente `CON` |
| `3` | interface serial, `AUX` |
| `4` | impressora, `PRN` |

Esse modelo aproxima arquivos e dispositivos: o mesmo fluxo de chamada pode operar sobre teclado, tela, serial, impressora ou arquivo.

## Ver também
- [[BIOS do IBM PC]]
- [[Tabela de Vetores de Interrupção]]
- [[Chamada do Sistema]]
- [[Programa TSR DOS]]
- [[Sistema Operacional]]
- [[Sistema de Arquivos]]
