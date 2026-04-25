---
title: Sistema Decimal
aliases:
  - base 10
  - sistema de base dez
  - numeração decimal
  - valor posicional decimal
tags:
  - computação/fundamentos
date: 2026-04-24
source: "desconhecida"
---

%% criado pelo Codex %%
%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Fundamentos]]

# Sistema Decimal

## Definição
Sistema de numeração posicional de **base 10**. Cada posição representa uma potência de 10, e o valor de um número depende tanto dos algarismos quanto da posição de cada algarismo.

## Relações (SPO)
- Sistema Decimal → usa → dez algarismos (`0` a `9`)
- Sistema Decimal → agrupa → quantidades em potências de 10
- Sistema Decimal → contrasta com → [[Dados Binários]]
- [[Conversão de Bases]] → traduz → valores entre Sistema Decimal e outras bases

## Valor posicional

Em `60879`, cada dígito tem peso diferente:

| Dígito | Peso | Contribuição |
|---|---:|---:|
| `6` | 10⁴ | 60000 |
| `0` | 10³ | 0 |
| `8` | 10² | 800 |
| `7` | 10¹ | 70 |
| `9` | 10⁰ | 9 |

O número é a soma das contribuições posicionais.

Outro exemplo clássico — `1989`:
$$1989_{10} = 1 \times 10^3 + 9 \times 10^2 + 8 \times 10^1 + 9 \times 10^0$$

## Princípio geral
O primeiro algarismo de qualquer sistema tem valor 1 (= base⁰). O fator pelo qual o valor cresce de uma coluna para a próxima é igual à **base**. Trocar a base muda os pesos das posições, não o princípio matemático.

| Sistema | Base | Algarismos | Fator por posição |
|---|---|---|---|
| Decimal | 10 | 0–9 | ×10 |
| [[Dados Binários\|Binário]] | 2 | 0–1 | ×2 |
| [[Octal]] | 8 | 0–7 | ×8 |
| [[Hexadecimal]] | 16 | 0–F | ×16 |

## Precisão finita em computadores

Computadores operam com números de **precisão finita e fixa** — diferente da aritmética humana, onde podemos usar tantos dígitos quantos forem necessários. A quantidade de memória para armazenar um número é fixada no projeto do hardware.

> Um físico pode escrever 10⁷⁸ sem se preocupar com os 79 dígitos necessários. Um computador não tem esse luxo — a representação é limitada pelo tamanho do registrador ou da palavra de memória.

## Ver também
- [[Conversão de Bases]]
- [[Dados Binários]]
- [[Octal]]
- [[Hexadecimal]]
- [[Sistemas de Representação]]

