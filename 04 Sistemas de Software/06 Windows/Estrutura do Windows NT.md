---
title: Estrutura do Windows NT
aliases:
  - arquitetura do Windows NT
  - NTOS
  - ntoskrnl.exe
tags:
  - computação/sistemas
  - sistemas-operacionais/windows
source: "Sistemas Operacionais Modernos, cap. 11"
created_by: Codex
---

# Estrutura do Windows NT

## Definição

Arquitetura interna do Windows em camadas: modo usuário (subsistemas, serviços, aplicações) sobre modo núcleo (HAL + NTOS + drivers).

## Modo núcleo

### HAL (Hardware Abstraction Layer)

Isola detalhes de hardware específicos (controladores de interrupção, barramentos, DMA). Permite que o restante do núcleo seja portável entre arquiteturas.

### NTOS (`ntoskrnl.exe`)

Carregado na inicialização. Tem duas subcamadas internas:

- **Executivo**: contém a maioria dos serviços. Gerenciadores:
  - [[Gerenciador de Objetos do Windows]] — namespace, descritores, ciclo de vida de objetos
  - Gerenciador de processos e threads
  - [[Gerenciamento de Memória no Windows]] — memória virtual, seções, paginação
  - Gerenciador de E/S — IRP, pilhas de drivers, plug-and-play, energia
  - Monitor de segurança (SRM) — checagem de acesso, auditoria
  - [[Cache do Windows]] — visões de arquivos em memória do núcleo
  - Gerenciador de configuração — registro do Windows
  - LPC — comunicação local entre processos no modo usuário

- **Núcleo** (camada interna do NTOS): implementa fundamentos de sincronização (spinlocks, DPC, APC), tratadores de interceptação, interrupções e escalonamento primitivo de threads.

### Drivers

Executam em modo núcleo. Incluem: drivers de hardware, sistemas de arquivos, filtros (antivírus), gerenciadores de volume, drivers de rede, serviços de núcleo sem hardware associado.

## Modo usuário

- **Subsistema Win32** (`csrss.exe`): implementa o ambiente Win32 em modo usuário. É o único subsistema restante com suporte completo.
- **Serviços**: processos que implementam funcionalidades do SO; gerenciados pelo SCM (`services.exe`). Comunicam com clientes via RPC ou named pipes.
- **Aplicações**: usam DLLs (kernel32, advapi32, user32, gdi32) para acessar o sistema.

## Inicialização

1. Firmware/BIOS → bootmgr
2. bootmgr → winload.exe
3. winload.exe carrega `ntoskrnl.exe`, `hal.dll` e drivers de boot da colmeia SYSTEM do [[Programação no Windows|registro]]
4. NTOS inicializa gerenciadores em sequência

## Relações (SPO)

- Estrutura do Windows NT -> inclui -> [[HAL]]
- Estrutura do Windows NT -> organiza -> [[Gerenciador de Objetos do Windows]]
- NTOS -> fornece -> chamadas de sistema
- Executivo -> contém -> gerenciadores especializados
- Drivers do Windows -> executam em -> modo núcleo
- Modo usuário -> acessa núcleo via -> [[Programação no Windows|DLLs e APIs]]

