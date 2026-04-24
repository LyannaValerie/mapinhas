---
title: Memória de Massa
aliases:
  - armazenamento secundário
  - unidades de armazenamento
  - secondary storage
tags:
  - computação/fundamentos
date: 2026-04-06
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Hardware]]

# Memória de Massa

## Definição
Nome genérico para dispositivos que armazenam dados e programas de forma **persistente** (não apagada ao desligar o computador). Equivalem ao módulo **Meio Externo de Gravação (R)** do [[Modelo de Von Neumann]].

## Relações (SPO)
- Memória de Massa → armazena → dados e programas de forma permanente
- Memória de Massa → equivale a → R do [[Modelo de Von Neumann]]
- Memória de Massa → transfere dados para → [[Memória]] (RAM) antes do uso pelo [[Processador]]
- Memória de Massa → contrasta com → [[Memória]] (RAM) — volátil

## Por que é necessária

> [!warning] RAM é volátil
> O conteúdo da [[Memória]] (RAM) é **apagado quando o computador é desligado**. Para que dados e programas persistam entre sessões, é necessário armazená-los em memória de massa.

O fluxo é: dados residem na memória de massa → quando necessários, são transferidos para a [[Memória]] (RAM) → o [[Processador]] os acessa a partir da RAM.

## Tipos de dispositivos

| Tipo | Exemplos |
|---|---|
| Disco rígido (HD) | HDD SATA, SSD NVMe |
| Estado sólido | SSD, pen drive |
| Óptico | CD, DVD, Blu-ray |
| Flash removível | Pen drive, cartão de memória |
| Legado | Disquete, fita magnética, cartão perfurado |

> [!note] Denominação
> Apesar do nome "memória de massa", estes dispositivos são de **armazenamento**, não de memória no sentido de Von Neumann. Ver [[Memória#Distinção Memória vs Armazenamento]].

## Ver também
- [[Memória]]
- [[Meio Externo de Gravação]]
- [[Processador]]
- [[PC]]
- [[Modelo de Von Neumann]]
- [[Disco Rígido]] — HD/HDD: geometria, SMART, interfaces, mercados
- [[SSD]] — NVMe, wear leveling, TRIM, formatos M.2/PCIe
- [[Memória Flash]] — NAND: base dos SSDs, pen drives e cartões de memória
