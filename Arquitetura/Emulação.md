---
title: Emulação
aliases:
  - emular
  - emulador
  - emulation
tags:
  - computação/arquitetura
  - computação/sistemas-operacionais
date: 2026-04-18
---

# Emulação

## Definição
Capacidade de um sistema de **imitar o comportamento de outro**, executando programas binários de uma arquitetura diferente sem modificação. O sistema emulado ("hóspede") desconhece que não está rodando no hardware original.

Distingue-se de [[Máquina Virtual]]: a VM abstrai o mesmo hardware para múltiplos SOs; a emulação abstrai um hardware *diferente*.

## Relações (SPO)
- Emulação → imita → comportamento de hardware ou software de outra arquitetura
- Emulador → interpreta → instruções do sistema hóspede uma a uma (ou via JIT)
- Emulação via microprograma → implementada por → [[Microprogramação|microcódigo]]
- Emulação → permite → migração de software sem recompilação
- Emulação → diferente de → [[Máquina Virtual]] (VM abstrai mesmo hardware)

## Exemplo histórico — IBM System/360 (1964)
O IBM tinha dois computadores incompatíveis de sucesso: o **7094** (científico) e o **1401** (comercial). Ao lançar o System/360, a IBM incluiu **microprogramas de emulação** que imitavam cada máquina — clientes migraram para o 360 sem reescrever seu software existente.

> [!example] Mecanismo
> O microcódigo do 360 buscava instruções no formato binário da 1401, decodificava e executava a operação equivalente no hardware do 360. Para o programa, era como se estivesse rodando na 1401 original.

## Tipos de emulação

| Tipo | Mecanismo | Desempenho |
|---|---|---|
| Interpretação pura | Busca e executa instrução por instrução | Lento |
| Compilação JIT | Traduz blocos de código em tempo real | Mais rápido |
| Microcódigo | Implementado em firmware do processador | Transparente ao SO |
| Camada de compatibilidade (SO) | Traduz chamadas de sistema | Custo baixo se ISA igual |

## Usos modernos
- Emulação de consoles de videogame (ex: NES, PlayStation em PC)
- Execução de aplicações ARM em x86 (ex: Rosetta 2 no macOS)
- Emulação de hardware legado em ambientes virtualizados

## Ver também
- [[Máquina Virtual]]
- [[Microprogramação]]
- [[Tradução e Interpretação]]
- [[História da Computação]]
- [[Sistema Operacional]]
- [[ISA]]
