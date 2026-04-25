---
title: Ambiente Freestanding C
aliases:
  - freestanding C
  - ambiente freestanding
tags:
  - computação/sistemas
  - computação/programação-c
source: "Operating Systems: From 0 to 1, cap. 8"
created_by: Codex
---

# Ambiente Freestanding C

Nota criada pelo Codex.

## Definição

**Ambiente freestanding C** é um ambiente de execução sem sistema operacional provendo biblioteca padrão completa, alocação de recursos e chamadas de sistema. É o caso típico de kernel, bootloader e código bare metal.

## Contraste com ambiente hosted

Ambiente *hosted* fornece rotinas dependentes do sistema, como E/S, arquivos, `printf`, `malloc` e inicialização de runtime. Em freestanding, essas capacidades precisam ser evitadas, substituídas ou implementadas pelo próprio sistema.

## Relações (SPO)

- Ambiente hosted → depende de → sistema operacional
- Ambiente freestanding → aparece em → [[Bootloader]]
- Ambiente freestanding → aparece em → [[Kernel]]
- Biblioteca padrão C completa → pressupõe → ambiente hosted

