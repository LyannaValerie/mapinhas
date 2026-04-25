---
title: Windows 8
aliases:
  - Windows 8.1
  - Windows moderno
tags:
  - computação/sistemas
  - sistemas-operacionais/windows
source: "Sistemas Operacionais Modernos, cap. 11"
created_by: Codex
---

# Windows 8

## Definição

[[Sistema Operacional]] da família Windows NT, executado em PCs, laptops, tablets, servidores e Xbox. Combina compatibilidade histórica com Win32, arquitetura NT em modo núcleo e a nova camada WinRT para aplicações Metro/Store.

## Contexto

- Versão mais recente ao tempo do livro: Windows 8.1
- Código: ~70 milhões de linhas de C/C++ (herdado de Vista, expandido no 7 e 8)
- Cobre desktops corporativos, servidores, tablets e plataformas em nuvem (Azure)

## Arquitetura resumida

```
Aplicações Win32 / Aplicações WinRT (Metro)
      ↓                    ↓
  kernel32.dll          WinRT (COM estendido)
      ↓                    ↓
     ntdll.dll   ←→   csrss.exe (Win32 subsystem)
          ↓
    NTOS (ntoskrnl.exe)
    Executivo + Núcleo + HAL
          ↓
        Drivers
```

## Componentes relacionados

- [[História do Windows]] — evolução das quatro eras
- [[Programação no Windows]] — API nativa, Win32, WinRT, registro
- [[Estrutura do Windows NT]] — NTOS, HAL, executivo, boot
- [[Gerenciador de Objetos do Windows]] — namespace, objetos, descritores
- [[Processos e Threads no Windows]] — processo, thread, job, fiber
- [[Gerenciamento de Memória no Windows]] — VAD, seções, paginação
- [[Cache do Windows]] — visões de arquivo no espaço do núcleo
- [[Entrada e Saída no Windows]] — IRP, pilhas de drivers, PnP
- [[Sistema de Arquivos do Windows NT]] — NTFS, MFT, streams
- [[Gerenciamento de Energia no Windows]] — S/D-states, hibernação
- [[Segurança no Windows 8]] — SID, token, ACL, UAC, ASLR

## Relações (SPO)

- Windows 8 -> pertence a -> família Windows NT
- Windows 8 -> expõe -> [[Programação no Windows]]
- Windows 8 -> implementa -> [[Estrutura do Windows NT]]
- Windows 8 -> usa -> [[Sistema de Arquivos do Windows NT]]
- Windows 8 -> introduz -> WinRT e connected standby

