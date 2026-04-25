---
title: Entrada e Saída no Windows
aliases:
  - E/S no Windows
  - I/O manager do Windows
  - IRP
tags:
  - computação/sistemas
  - sistemas-operacionais/windows
  - sistemas-operacionais/entrada-saída
source: "Sistemas Operacionais Modernos, cap. 11"
created_by: Codex
---

# Entrada e Saída no Windows

## Definição

Subsistema de [[Entrada e Saída em Sistemas Operacionais|E/S]] do Windows que organiza drivers, dispositivos, sistemas de arquivos, volumes, filtros e serviços de núcleo em um modelo extensível e assíncrono.

## IRP (I/O Request Packet)

Toda operação de E/S é representada por um **IRP**: estrutura de dados que descreve a operação (tipo, buffer, offset, flags). O gerenciador de E/S cria um IRP e o passa pela pilha de drivers. Cada driver pode:
- Processar e completar o IRP
- Passar adiante para o próximo driver na pilha
- Criar sub-IRPs

Quando o IRP completa, rotinas de conclusão (*completion routines*) são chamadas na ordem inversa.

## Pilha de drivers

Dispositivos são representados por cadeias de **Device Objects** formando uma pilha. Exemplo para um disco SATA:

```
[Filtro de volume] → [Sistema de arquivos (NTFS)] → [Driver de partição] → [Driver de disco] → [Driver do controlador SATA]
```

Cada camada pode modificar, rejeitar ou repassar o IRP. Isso permite inserir filtros (antivírus, criptografia, replicação) sem alterar drivers existentes.

## Plug-and-play

O gerenciador PnP enumera barramentos (PCI, USB, SATA, etc.) e instala drivers automaticamente. O Windows distribuiu ~1 milhão de drivers binários diferentes para cobrir o ecossistema de hardware.

## E/S assíncrona

O modelo de E/S é fundamentalmente assíncrono: a operação é iniciada, o thread continua, e a conclusão é notificada via:
- Evento de sincronização no IRP
- IOCP (I/O Completion Port) — fila de conclusão escalável
- APC (Async Procedure Call) no thread do chamador

## Relações (SPO)

- Entrada e Saída no Windows -> usa -> drivers
- IRP -> representa -> operação de E/S em trânsito
- Driver de sistema de arquivos -> implementa -> [[Sistema de Arquivos do Windows NT]]
- Filtro antivírus -> intercepta -> operações de E/S via pilha
- Gerenciador de E/S -> coordena -> pilhas de drivers

