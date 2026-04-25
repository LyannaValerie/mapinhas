---
title: Driver de Dispositivo DOS
aliases:
  - device driver DOS
  - driver DOS
  - driver de caractere DOS
  - driver de bloco DOS
tags:
  - computação/sistemas
date: 2026-04-24
created_by: Codex
source: desconhecida
---

# Driver de Dispositivo DOS

Nota criada pelo Codex.

## Definição

Driver de dispositivo DOS é o módulo que integra um dispositivo ao [[MS-DOS]], expondo operações de entrada, saída, controle e estado por uma interface esperada pelo sistema.

## Relações (SPO)
- Driver DOS → abstrai → dispositivo físico
- Driver de caractere → opera sobre → fluxo de bytes
- Driver de bloco → opera sobre → blocos endereçáveis
- DOS → chama → funções do driver
- Driver → pode atender → dispositivo padrão
- [[BIOS do IBM PC]] → fornece → rotinas de baixo nível usadas por drivers

---

## Tipos

| Tipo | Unidade operacional | Exemplos conceituais |
|---|---|---|
| Caractere | byte ou fluxo sequencial | console, serial, impressora |
| Bloco | setor ou bloco endereçável | disco, unidade de armazenamento |

Drivers permitem que DOS trate dispositivos heterogêneos por contratos de chamada estáveis.

## Ver também
- [[MS-DOS]]
- [[Sistema de Arquivos]]
- [[Arquivo]]
- [[BIOS do IBM PC]]
- [[Interrupção]]

