---
title: Estrutura Monolítica de Sistema Operacional
aliases:
  - sistema monolítico
  - kernel monolítico
tags:
  - computação/sistemas
source: "Sistemas Operacionais Modernos, cap. 1"
created_by: Codex
date: 2026-04-25
---

%% criado pelo Codex %%

# Estrutura Monolítica de Sistema Operacional

## Definição

Organização em que o sistema operacional inteiro roda como um único programa em [[Modo Kernel e Modo Usuário|modo núcleo]]. Toda chamada de serviço é tratada por rotinas dentro do mesmo espaço de endereçamento privilegiado.

## Modelo estrutural (Tanenbaum §1.7.1)

Tanenbaum descreve a estrutura monolítica com três camadas funcionais:

| Camada | Função |
|---|---|
| Programa principal | Recebe chamadas de sistema e invoca a rotina de serviço correspondente |
| Rotinas de serviço | Uma por chamada de sistema; executa a operação requisitada |
| Rotinas utilitárias | Compartilhadas entre rotinas de serviço (e.g., copiar dados de espaço de usuário) |

Quando uma [[Chamada do Sistema]] ocorre, o hardware faz TRAP para o núcleo. O SO indexa a tabela de chamadas pelo número da syscall e despacha a rotina de serviço correspondente.

## Extensões carregáveis

Além do núcleo base, muitos sistemas monolíticos permitem extensões carregadas sob demanda:
- **UNIX/Linux**: bibliotecas compartilhadas (`.so`)
- **Windows**: DLLs (Dynamic Link Libraries, `.dll`) — `C:\Windows\system32` contém mais de 1.000 delas

## Propriedades

- Rotinas internas podem chamar outras diretamente — sem IPC, sem troca de contexto.
- Custo de chamada interna é baixo: um `call` simples.
- Acoplamento alto: falha em uma rotina pode corromper o estado do núcleo inteiro.
- Debugging e manutenção são mais difíceis quanto maior o kernel.

## Exemplos

Linux e FreeBSD são kernels monolíticos com suporte a módulos carregáveis, o que permite adicionar drivers sem recompilar o kernel.

## Relações (SPO)

- Estrutura Monolítica → executa em → [[Espaço de Usuário e Espaço de Kernel|espaço de kernel]]
- Estrutura Monolítica → organiza → programa principal + rotinas de serviço + rotinas utilitárias
- Estrutura Monolítica → privilegia → desempenho de chamada interna
- Estrutura Monolítica → reduz → isolamento entre componentes do kernel
- Estrutura Monolítica → contrasta com → [[Micronúcleo]]
- Estrutura Monolítica → contrasta com → [[Sistema em Camadas]]
- Linux → implementa → estrutura monolítica modular

