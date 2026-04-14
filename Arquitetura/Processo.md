---
title: Processo
aliases:
  - process
  - contexto de processo
  - context switching
  - troca de contexto
  - thread
  - kernel
tags:
  - computação/arquitetura
date: 2026-04-06
---

# Processo

## Definição
Abstração do [[Sistema Operacional]] para um **programa em execução**. Cada processo tem a ilusão de uso exclusivo do [[Processador]], da [[Memória]] principal e dos dispositivos de E/S — mesmo quando vários processos rodam simultaneamente no mesmo sistema.

## Relações (SPO)
- Processo → é uma abstração de → [[Sistema Operacional]]
- Processo → abstrai → [[Processador]] + [[Memória]] + dispositivos de E/S
- Processo → seu estado é → contexto (PC + registradores + memória)
- Processo → gerenciado por → [[#Kernel|kernel]] do SO
- Processo → pode conter → múltiplas [[#Threads|threads]]

---

## A ilusão do processo

Quando um programa roda em um sistema moderno, o SO fornece três ilusões:
1. O programa **parece ter o processador exclusivo** — suas instruções executam sem interrupção aparente
2. O programa **parece ter a memória exclusiva** — seu código e dados parecem ser os únicos na memória
3. O programa **parece ter os dispositivos de E/S exclusivos**

Essas ilusões são possíveis graças ao conceito de processo — uma das ideias mais importantes e bem-sucedidas da ciência da computação.

---

## Contexto

O **contexto** de um processo é todo o estado necessário para que ele execute:
- Valor atual do **PC** (program counter) — [[Processador#Program Counter (PC) — Contador de Programa|ver definição]]
- Conteúdo do **arquivo de registradores**
- Conteúdo da **[[Memória]] principal**

---

## Troca de contexto (Context Switching)

Como um único processador pode executar múltiplos processos, o SO realiza **troca de contexto**:

```
Processo A em execução
    ↓ SO decide trocar
SO salva contexto de A
SO restaura contexto de B
Processo B em execução
    ↓ (A aguarda)
...
```

**Fluxo no exemplo `hello`:**
1. [[Shell]] está rodando, aguardando comando
2. Usuário executa `./hello` → shell faz uma **[[Chamada do Sistema]]** passando controle ao SO
3. SO **salva o contexto do shell**, cria processo `hello` com seu contexto
4. `hello` executa, imprime mensagem, encerra
5. SO **restaura o contexto do shell** → shell retoma exatamente onde parou

> [!info] Processos concorrentes vs. paralelos
> Em sistemas uniprocessador: processos são **concorrentes** (alternância rápida dá ilusão de simultaneidade). Em sistemas multicore: processos podem ser **paralelos** (execução verdadeiramente simultânea em núcleos diferentes).

---

## Kernel

O **kernel** é a parte do código do SO que está **sempre residente na memória**. É responsável por:
- Gerenciar todos os processos
- Realizar trocas de contexto
- Executar operações solicitadas via [[Chamada do Sistema]]

> [!note] Kernel não é um processo separado
> O kernel é uma **coleção de código e estruturas de dados** usada pelo sistema para gerenciar processos — não um processo em si.

Quando um programa de aplicação precisa de uma ação do SO (ex: ler ou escrever um arquivo), ele executa uma **instrução de chamada de sistema** que transfere controle ao kernel. O kernel executa a operação e retorna ao programa.

---

## Threads

Um processo pode conter múltiplas **threads** — unidades de execução que rodam no contexto do processo, **compartilhando** o mesmo código e dados globais.

| | Processo | Thread |
|---|---|---|
| Código e dados | Isolados | **Compartilhados** com outras threads do mesmo processo |
| Overhead de criação | Alto | Baixo |
| Compartilhamento de dados | Complexo (IPC) | Simples (memória compartilhada) |
| Concorrência | Sim | Sim (mais eficiente que múltiplos processos) |

**Por que threads são importantes:**
- Servidores de rede com alta concorrência
- Programas que precisam de múltiplos fluxos de execução
- Aproveitamento de **múltiplos núcleos** de processador (multicore)

> [!info] Threads e paralelismo real
> Em sistemas **uniprocessadores**, threads são *concorrentes* — o processador alterna entre elas rapidamente, dando a ilusão de simultaneidade. Em sistemas **multicore** e com **hyperthreading**, múltiplas threads podem executar *em paralelo* de verdade. Ver [[Concorrência e Paralelismo]].

---

## Ver também
- [[Sistema Operacional]] — o processo é uma das três abstrações fundamentais do SO
- [[Memória Virtual]] — cada processo tem sua própria visão da memória
- [[Chamada do Sistema]] — como processos solicitam serviços ao kernel
- [[Processador]] — onde as instruções do processo são executadas
- [[Shell]] — exemplo de processo de aplicação
- [[Concorrência e Paralelismo]] — multicore, hyperthreading e ILP
