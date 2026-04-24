---
title: Notação Polonesa Invertida
aliases:
  - RPN
  - Reverse Polish Notation
  - notação pós-fixa
  - postfix notation
tags:
  - computação/arquitetura
  - computação/fundamentos
date: 2026-04-18
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Notação Polonesa Invertida

## Definição
Sistema de notação matemática em que o **operador aparece após seus operandos** (notação pós-fixa). Proposta por Jan Łukasiewicz; popularizada na computação por sua afinidade natural com estruturas de [[Pilha]].

| Notação | Forma | Exemplo |
|---|---|---|
| Infixa (convencional) | operador entre operandos | `(A + B) × C` |
| **Polonesa Invertida (RPN)** | **operador após operandos** | `A B + C ×` |
| Polonesa (prefixada) | operador antes dos operandos | `× + A B C` |

## Relações (SPO)
- RPN → elimina → parênteses e regras de precedência
- RPN → avaliada por → algoritmo de [[Pilha]] (empilha operandos, aplica operador)
- RPN → base para → endereçamento de pilha ([[Modos de Endereçamento#Endereçamento de Pilha|modo de 0 endereços]])
- RPN → usada em → calculadoras HP, linguagem Forth, bytecode JVM ([[IJVM]])
- RPN → operadores aparecem em → ordem de execução real

## Algoritmo de Avaliação via Pilha

Percorrer a expressão da esquerda para a direita:
- **Operando** → empilha
- **Operador** → desempilha dois valores, aplica operação, empilha resultado

> [!example] `(8 + 2 × 5) / (1 + 3 × 2 − 4)` em RPN: `8 2 5 × + 1 3 2 × + 4 − /`
>
> | Símbolo | Ação | Pilha |
> |---|---|---|
> | 8 | empilha | [8] |
> | 2 | empilha | [8, 2] |
> | 5 | empilha | [8, 2, 5] |
> | × | pop 5, pop 2, empilha 10 | [8, 10] |
> | + | pop 10, pop 8, empilha 18 | [18] |
> | 1 | empilha | [18, 1] |
> | 3 | empilha | [18, 1, 3] |
> | 2 | empilha | [18, 1, 3, 2] |
> | × | pop 2, pop 3, empilha 6 | [18, 1, 6] |
> | + | pop 6, pop 1, empilha 7 | [18, 7] |
> | 4 | empilha | [18, 7, 4] |
> | − | pop 4, pop 7, empilha 3 | [18, 3] |
> | / | pop 3, pop 18, empilha 6 | [6] |
>
> Resultado: **6**

## Vantagens
1. **Sem parênteses** — nenhuma expressão exige parênteses, independentemente da complexidade
2. **Ordem de execução explícita** — operadores aparecem na sequência em que serão aplicados
3. **Implementação trivial** — uma pilha é suficiente; sem parser de precedência

## Uso em Arquitetura de Computadores
Máquinas com **endereçamento de pilha** (0 endereços) operam naturalmente em RPN: instruções como `IADD` retiram dois operandos do topo da [[Pilha]], somam e empilham o resultado — sem especificar endereços. Ver [[Modos de Endereçamento]] e [[IJVM]].

## Ver também
- [[Pilha]]
- [[Modos de Endereçamento]]
- [[IJVM]]
- [[ULA]]
