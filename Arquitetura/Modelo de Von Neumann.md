---
title: Modelo de Von Neumann
aliases:
  - arquitetura de Von Neumann
  - computador de programa armazenado
tags:
  - computação/arquitetura
  - computação/histórico
date: 2026-04-06
---

# Modelo de Von Neumann

## Definição
Arquitetura de computador criada por **John von Neumann** em **1945**, durante o desenvolvimento do [[EDVAC]]. É também chamado de **computador de programa armazenado**.

## Relações (SPO)
- Modelo de Von Neumann → criado por → John von Neumann
- Modelo de Von Neumann → criado em → 1945
- Modelo de Von Neumann → desenvolvido durante → [[EDVAC]]
- Modelo de Von Neumann → base de → [[Computador]] modernos
- Modelo de Von Neumann → viabiliza → [[Programa Armazenado]]
- Modelo de Von Neumann → separa → [[Hardware]] e [[Software]]
- Modelo de Von Neumann → substituiu → [[Computador de Programa Fixo]]
- Modelo de Von Neumann → composto por → [[ULA]], [[Processador#Central de Controle|CC]], Memória, Entrada/Saída, Gravação

## Mapa simbólico
```mapa
# Sucessão histórica
[[Computador de Programa Fixo]] → ModeloVonNeumann  : 1945
[[EDVAC]] ⇒ ModeloVonNeumann                        : originou

# Composição da arquitetura
ModeloVonNeumann ⊗ {[[ULA]], CC, [[Memória]], Entrada, Saída, [[Meio Externo de Gravação]]}

# Viabilizações
ModeloVonNeumann ⇒ [[Programa Armazenado]]
ModeloVonNeumann ⇒ [[Computador]]
ModeloVonNeumann ⇒ ([[Hardware]] ∧ ¬[[Software]])   : separação conceitual

# Equivalência terminológica
[[ULA]] ≡ CA[VonNeumann]
```

> [!note] Legenda rápida
> `→` sucedido por · `⇒` viabiliza · `⊗` composto por · `≡` equivale a. Ver [[Linguagem Simbólica|MAPA]].

## Componentes da Arquitetura

| Componente original (Von Neumann) | Equivalente moderno |
|---|---|
| Central de Aritmética (CA) | [[ULA]] (ALU) |
| Central de Controle (CC) | Parte do [[Processador]] |
| Memória (M) | RAM / memória principal |
| Entrada | Teclado, mouse, etc. |
| Saída | Monitor, impressora, etc. |
| Meio Externo de Gravação (R) | HD, SSD, pen drive, etc. |

> [!note] Nomenclatura
> "Von Neumann" pronuncia-se **"fôn nóiman"**. Algumas bibliografias modernizam os termos e o esquema acima.

## Característica Principal
Permite que a programação do processador seja feita por instruções de um **programa** — ou seja, via [[Software]] — sem necessidade de reconfiguração física do [[Hardware]].

> Um **programa** é uma sequência de instruções e dados que dizem ao [[Computador]] o que se deseja que ele compute.

## Importância Histórica
Praticamente todos os computadores modernos são derivados deste modelo. Ele definiu a separação fundamental entre [[Hardware]] (parte física) e [[Software]] (parte lógica), conceito central da computação.

## Ponte Conceitual

> [!info] Ponte entre Hardware e Software
> Este modelo é a **ponte** entre [[Hardware]] e [[Software]]: ao definir o [[Programa Armazenado]], ele tornou possível modificar o comportamento do [[Computador]] sem alterar seus componentes físicos — unindo os dois conceitos num único sistema.

## Legado

> [!quote] O legado de Von Neumann
> Ao estudar processadores modernos, você verá muitas similaridades com este modelo. Nomes podem ter sido atualizados e os blocos podem ser mais complexos, mas a ideia básica é a mesma: o processador **busca instruções da memória**, **processa** essas instruções e **grava o resultado** em memória, em um dispositivo de armazenamento ou em um dispositivo de saída.

## Ver também
- [[EDVAC]]
- [[Programa Armazenado]]
- [[Computador de Programa Fixo]]
- [[Processador]]
- [[Memória]]
- [[Meio Externo de Gravação]]
- [[DMA]]
- [[Hardware]]
- [[Software]]
- [[Computador]]
