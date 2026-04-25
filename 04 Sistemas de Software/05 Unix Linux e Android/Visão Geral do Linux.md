---
title: Visão Geral do Linux
aliases:
  - Linux
  - núcleo Linux
  - kernel Linux
  - interfaces do Linux
tags:
  - computação/sistemas
source: "Sistemas Operacionais Modernos, cap. 10"
created_by: Codex
---

# Visão Geral do Linux

## Definição

Sistema operacional compatível com UNIX, centrado em um núcleo monolítico modular e em uma interface de chamadas de sistema usada por bibliotecas, utilitários e aplicações.

## Papel técnico

Linux separa a interface de usuário da implementação interna do núcleo. Programas normalmente acessam serviços por biblioteca C, shell ou utilitários; o núcleo gerencia processos, threads, memória, dispositivos, arquivos, rede e segurança.

## Relações (SPO)

- Linux -> expõe -> chamadas de sistema
- Biblioteca C -> encapsula -> chamadas de sistema Linux
- [[Shell]] -> inicia -> programas utilitários
- Núcleo Linux -> gerencia -> processos, memória, E/S e arquivos
- Linux -> usa -> módulos carregáveis do núcleo
- Chamada de sistema -> transita -> modo usuário para modo núcleo

## Objetivos de Design

Projetado por programadores, para programadores:
- **Simplicidade e elegância**: arquivo = coleção de bytes (sem tipos distintos)
- **Princípio da menor surpresa**: `ls A*` e `rm A*` seguem a mesma semântica
- **Poder e flexibilidade**: um programa faz apenas uma coisa, e faz bem
- **Combinar ferramentas**: pipes e redirecionamento permitem composição
- **Economia de digitação**: `cp`, `rm`, `ls` em vez de palavras longas

## 3 Interfaces do Linux

```
┌──────────────────────────────────────────┐
│         Usuários / Aplicações            │
├──────────────────────────────────────────┤
│  Interface de usuário (shell, GUI, etc.) │
├──────────────────────────────────────────┤
│         Interface de biblioteca          │
│   (open, close, read, write, fork...)    │
├──────────────────────────────────────────┤
│    Interface de chamadas de sistema      │
├──────────────────────────────────────────┤
│   Núcleo (modo núcleo)                   │
│   processos | memória | FS | E/S | rede  │
├──────────────────────────────────────────┤
│   Hardware (CPU, memória, discos...)     │
└──────────────────────────────────────────┘
```

**Chamadas de sistema**: programas colocam argumentos em registradores (ou na pilha) e emitem instrução de desvio → modo usuário → modo núcleo. Biblioteca C fornece uma função por chamada de sistema.

## Shell e Utilitários

Shell é o interpretador de comandos. Funções básicas:
- Executar programas: `comando arg1 arg2`
- Flags com traço: `head -20 file`
- Redirecionamento: `comando < entrada > saída`
- Pipes: `grep ard f | sort | head`
- Segundo plano: `comando &`

Utilitários comuns:

| Comando | Função |
|---------|--------|
| `cat` | Concatena e imprime arquivos |
| `grep` | Busca padrões em arquivos |
| `ls` | Lista diretório |
| `make` | Compila e constrói binário |
| `od` | Dump octal de arquivo |
| `ps` | Lista processos em execução |
| `sort` | Ordena linhas |
| `wc` | Conta palavras/linhas/bytes |

## Estrutura do Núcleo

O núcleo Linux é monolítico (todo o SO no espaço de núcleo) mas suporta **módulos carregáveis**: drivers, sistemas de arquivos, protocolos de rede podem ser inseridos e removidos em tempo de execução.

Camadas internas:
- Manipuladores de interrupção (nível mais baixo)
- Roteador de interrupções
- Drivers de dispositivo
- Pilha de rede
- Gerenciamento de memória e processos
- Sistema de arquivos (VFS)

## Ver também

- [[História do UNIX e do Linux]]
- [[Processos no Linux]]
- [[Gerenciamento de Memória no Linux]]
- [[Sistema de Arquivos Linux]]
- [[Escalonamento no Linux]]
- [[Kernel]]
