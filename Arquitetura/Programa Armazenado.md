---
title: Programa Armazenado
aliases:
  - stored program
  - programa em memória
tags:
  - computação/arquitetura
date: 2026-04-06
---

# Programa Armazenado

## Definição
Conceito central do [[Modelo de Von Neumann]]: o programa (sequência de instruções e dados) é armazenado na memória do [[Computador]] e executado pelo processador — permitindo reprogramação via [[Software]] sem alteração física do [[Hardware]].

## Relações (SPO)
- Programa Armazenado → viabilizado por → [[Modelo de Von Neumann]]
- Programa Armazenado → separa → lógica de → configuração física
- Programa Armazenado → armazenado como → [[Dados Binários]]
- Programa Armazenado → executado por → [[Hardware]]
- Programa Armazenado → expresso em → [[Software]]

## Definição de Programa

> Um **programa** é uma sequência de instruções e dados que dizem ao [[Computador]] o que se deseja que ele compute.

## Importância
Este conceito é o que distingue computadores modernos de máquinas de propósito fixo: ao armazenar o programa junto aos dados, o mesmo [[Hardware]] pode executar tarefas completamente diferentes dependendo do [[Software]] carregado.

## Programas podem ser modificados como dados (Q9)

Uma consequência direta do [[Modelo de Von Neumann]] é que, se programa e dados habitam a mesma memória e são ambos sequências de bits, **um programa pode modificar suas próprias instruções em tempo de execução** — exatamente como modifica dados.

### Por que isso é útil?

**Exemplo clássico — aritmética em vetores:**
Um programa que soma 1000 elementos de um vetor poderia, em vez de conter um laço com verificação de índice a cada iteração, **gerar dinamicamente** as 1000 instruções de adição em sequência na memória e executá-las diretamente — eliminando o overhead de desvio condicional. O programa escreve suas próprias instruções como se fossem dados e depois as executa.

**Outros casos reais:**
| Caso | Como usa modificação de programa |
|---|---|
| **JIT (Just-In-Time)** | O compilador Java/Python gera código de máquina nativo em memória durante a execução e o executa imediatamente |
| **Otimização de laços** | Técnica *loop unrolling*: o programa duplica seu próprio corpo de laço para reduzir saltos |
| **Vírus polimórficos** | Modificam seu próprio código a cada cópia para escapar de detecção por assinatura |
| **Sistemas de plug-in** | Carregam e executam código novo sem reiniciar o programa principal |

> [!warning] Custo
> Código automodificável é difícil de depurar, de otimizar pelo compilador e pode criar vulnerabilidades de segurança (ex: injeção de código). Linguagens modernas e SOs restringem essa capacidade via proteção de páginas de memória.

## Ver também
- [[Modelo de Von Neumann]]
- [[Software]]
- [[Hardware]]
- [[Dados Binários]]
- [[Computador]]
- [[EDVAC]]
