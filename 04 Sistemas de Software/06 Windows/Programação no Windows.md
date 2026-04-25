---
title: Programação no Windows
aliases:
  - API do Windows
  - Win32
  - WinRT
  - API nativa do NT
tags:
  - computação/sistemas
  - sistemas-operacionais/windows
source: "Sistemas Operacionais Modernos, cap. 11"
created_by: Codex
---

# Programação no Windows

## Definição

Modelo de acesso aos recursos do Windows por APIs em modo usuário. As aplicações usam DLLs, serviços e subsistemas, sem acesso direto às interfaces nativas do núcleo.

## 1. API nativa do NT

Interface de chamadas de sistema implementada na camada executiva NTOS. **Não é documentada publicamente** pela Microsoft. Exposta via `ntdll.dll`. Funções prefixadas com `Nt*` ou `Zw*`.

Usada por: subsistemas (Win32), serviços de sistema, alguns drivers de modo usuário. Não deve ser usada diretamente por aplicações comuns — a Microsoft pode alterá-la entre versões.

## 2. Win32

Conjunto de APIs documentadas e estáveis que mapeiam sobre a API nativa. Implementadas em DLLs:

| DLL | Área |
|-----|------|
| `kernel32.dll` | processos, threads, memória, arquivos |
| `advapi32.dll` | registro, segurança, serviços |
| `user32.dll` | janelas, mensagens, entrada |
| `gdi32.dll` | gráficos 2D |

A maioria das funções Win32 de baixo nível tem correspondente direto na API nativa (`CreateFile` → `NtCreateFile`, etc.). A camada Win32 normaliza parâmetros (nomes de caminho canônicos, compatibilidade com nomes de dispositivo MS-DOS como `LPT:`).

## 3. WinRT

Ambiente de programação moderno introduzido no Windows 8, voltado a aplicações Metro/Store. Modelo de objeto baseado em COM estendido. Aplicações executam em sandbox com acesso restrito. Suporte a C++/CX, C#, JavaScript.

## 4. Registro do Windows

Banco hierárquico de configuração. Organizado em colmeias (*hives*), cada uma em um arquivo em `C:\Windows\system32\config\`:

| Colmeia | Conteúdo |
|---------|---------|
| `SYSTEM` | drivers de boot, parâmetros de inicialização |
| `SOFTWARE` | configuração de aplicações e componentes |
| `SAM` | contas de usuário locais |
| `SECURITY` | política de segurança local |
| `HKCU` (por usuário) | preferências do usuário atual |

Tipos de valor: `REG_DWORD`, `REG_SZ`, `REG_MULTI_SZ`, `REG_BINARY`, `REG_EXPAND_SZ`.

A colmeia SYSTEM é carregada pelo bootloader antes mesmo do núcleo para determinar quais drivers de boot usar.

## Relações (SPO)

- Programação no Windows -> usa -> DLLs
- Win32 -> abstrai -> API nativa do NT
- API nativa do NT -> acessa -> [[Estrutura do Windows NT]]
- Registro do Windows -> armazena -> configuração de sistema
- WinRT -> substitui parcialmente -> Win32 em apps Metro
- Bootloader -> carrega -> colmeia SYSTEM

