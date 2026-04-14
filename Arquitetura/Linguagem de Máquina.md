---
title: Linguagem de Máquina
aliases:
  - L0
  - machine language
  - linguagem de nível 0
  - instruções de máquina
tags:
  - computação/fundamentos
date: 2026-04-06
---

# Linguagem de Máquina

## Definição
Conjunto limitado de instruções simples que os circuitos eletrônicos de um computador podem reconhecer e executar **diretamente**, sem tradução ou interpretação. É o nível mais baixo da [[Níveis de Abstração|organização estruturada de computadores]] — denominado **L0**.

## Relações (SPO)
- Linguagem de Máquina → executada diretamente por → circuitos eletrônicos ([[Hardware]])
- Linguagem de Máquina → representada em → [[Dados Binários]]
- Linguagem de Máquina → base de → [[Níveis de Abstração]]
- Linguagem de Máquina → define → o [[Computador]] real (M0)
- Linguagem de Máquina → todos os programas → devem ser convertidos para ela antes da execução

## Características

- Instruções são **sequências de bits** — números — que possuem significado específico para o processador.
- O mesmo valor numérico pode ter significados **completamente diferentes** em linhas de processador distintas (ex.: `2Fh` = "somar" em uma linha, "desviar" em outra).
- Instruções são primitivas e simples — raramente mais complexas do que:
  - *Some dois números*
  - *Verifique se um número é zero*
  - *Copie dados de uma parte da [[Memória]] para outra*
- É definida pelos projetistas do computador, que buscam mantê-la **o mais simples possível** para reduzir complexidade e custo dos circuitos.
- É difícil e tedioso para humanos escreverem programas diretamente nela.

## Conjunto de Instruções
Todo processador possui um **conjunto de instruções** — tabela que relaciona cada instrução ao seu código numérico (ver [[ISA]]). Varia por linha de processadores; modelos mais novos podem ter instruções adicionais. Processadores x86 usam os conjuntos **x86** (instruções inteiras) e **x87** (ponto flutuante).

## Linguagens de Alto Nível e Conversão
Programadores geralmente escrevem em **linguagens de alto nível** (C, C++, Pascal, Visual Basic etc.), que o processador não entende diretamente. A conversão para linguagem de máquina ocorre de duas formas (ver [[Tradução e Interpretação]]):

| Método | Como funciona | Exemplos |
|---|---|---|
| **Compilação** | Programa inteiro convertido antes da execução | C, C++, Pascal |
| **Interpretação** | Linhas convertidas na medida em que são executadas | PHP, Python (clássico) |

## Relação com Programa

Todo [[Programa Armazenado|programa]] deve ser convertido para linguagem de máquina antes de ser executado. Essa conversão pode ocorrer por [[Tradução e Interpretação|tradução ou interpretação]].

## Ver também
- [[Níveis de Abstração]]
- [[Tradução e Interpretação]]
- [[Programa Armazenado]]
- [[Dados Binários]]
- [[Hardware]]
- [[Processador]]
