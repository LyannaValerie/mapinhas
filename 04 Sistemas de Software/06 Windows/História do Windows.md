---
title: História do Windows
aliases:
  - eras do Windows
  - evolução do Windows
tags:
  - computação/sistemas
  - sistemas-operacionais/windows
source: "Sistemas Operacionais Modernos, cap. 11"
created_by: Codex
---

# História do Windows

## Quatro eras

O desenvolvimento do Windows para PCs e servidores pode ser dividido em quatro eras técnicas, cada uma com base de execução substancialmente diferente:

1. **[[MS-DOS]]** — single-tasking, 16 bits, modo real x86
2. **Windows baseado no MS-DOS** — GUI sobre DOS, 16+32 bits híbrido
3. **Windows baseado em NT** — kernel NT puro, 32 bits, memória virtual, proteção
4. **Windows moderno** — arquitetura NT com interfaces WinRT, Metro, tablet/mobile

## 1. Década de 1980: MS-DOS

A IBM produzia um PC baseado no Intel 8088 e buscou sistema operacional. Gary Kildall (Digital Research, CP/M) não estava disponível; a IBM então procurou a Microsoft. A Microsoft comprou um clone de CP/M chamado QDOS (Quick and Dirty OS) / 86-DOS da Seattle Computer Products por \$75 mil, licenciou ao nome MS-DOS e o entregou à IBM como PC-DOS.

MS-DOS era monotarefa, sem proteção de memória, espaço de endereçamento flat de 1 MB. Toda a interface era por chamadas ao BIOS via `INT`.

## 2. Década de 1990: Windows baseado no MS-DOS

- **Win 3.0 / 3.1**: multitarefa cooperativa; adoção em massa após Win 3.0 (1990).
- **Windows 95/98/ME**: kernel híbrido — código DOS de 16 bits + código Win32 de 32 bits. A arquitetura era frágil, sem proteção entre processos. Windows 98SE e ME chegaram a ~15 milhões de linhas de código.

## 3. Década de 2000: Windows baseado em NT

- **NT 3.1 (1993)**: primeiro lançamento NT. Arquitetura modular, memória virtual, modo protegido puro.
- **NT 4.0 (1996)**: incorporou subsistema gráfico ao modo núcleo para desempenho.
- **Windows 2000**: NT com suporte a Active Directory, Plug-and-Play aprimorado.
- **Windows XP**: fusão da linha NT com a linha consumidor. Passou a ~40 milhões de linhas de código.

## 4. Windows Vista e Windows moderno

- **Vista**: reescrita profunda. UAC (User Account Control), ASLR, DEP, assinatura de drivers. Código cresceu para ~70 milhões de linhas.
- **Windows 7**: refinamento do Vista, melhor desempenho e aceitação.
- **Windows 8**: introduziu WinRT e interface Metro para tablets; manteve núcleo NT e Win32. Versão 8.1 disponibilizou atualizações incrementais.

## Relações (SPO)

- História do Windows -> inclui -> [[MS-DOS]]
- Windows NT -> substitui gradualmente -> Windows baseado em MS-DOS
- Windows moderno -> herda -> arquitetura Windows NT
- [[Windows 8]] -> pertence a -> Windows moderno
- Windows Vista -> introduz -> UAC e ASLR

