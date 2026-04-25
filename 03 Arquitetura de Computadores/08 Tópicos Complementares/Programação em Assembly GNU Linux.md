---
title: Programação em Assembly GNU Linux
aliases:
  - assembly GNU Linux
  - assembly x86 GNU Linux
tags:
  - computação/fundamentos
source: Programming from the Ground Up (pgubook), metadados bibliográficos completos ausentes no texto local
created_by: Codex
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Programação em Assembly GNU Linux

## Definição

Programação em assembly GNU/Linux usa a [[Linguagem Assembly]] como forma explícita de estudar execução real: registradores, memória, pilha, chamadas de sistema, montagem e ligação.

## Relações (SPO)
- Assembly GNU/Linux → usa → [[Registradores x86]]
- Assembly GNU/Linux → depende de → [[Chamada do Sistema]]
- Código assembly → é traduzido por → assembler
- Objeto montado → é combinado por → [[Ligador]]
- Programa em usuário → solicita serviço via → [[Chamada do Sistema]]

## Escopo didático

O foco não é portabilidade. A escolha de x86 com GNU/Linux fixa uma plataforma concreta para observar como programa, sistema operacional e processador cooperam.

## Fluxo básico

1. Escrever fonte assembly.
2. Montar para objeto.
3. Ligar para executável.
4. Executar sob GNU/Linux.
5. Observar estado em registradores, memória e retorno ao sistema.

## Ver também

- [[Linguagem Assembly]]
- [[Registradores x86]]
- [[Chamada do Sistema]]
- [[Processo de Montagem]]

## Nota de integração

criado pelo Codex
