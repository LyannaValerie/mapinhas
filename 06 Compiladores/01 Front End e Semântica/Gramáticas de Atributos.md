---
title: Gramáticas de Atributos
tags:
  - compiladores
  - semântica
aliases:
  - attribute grammar
  - gramática atribuída
  - attributed context-free grammar
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Compiladores]]

# Gramáticas de Atributos

CFG aumentada com um conjunto de **regras** que especificam computações. Cada regra define um **atributo** em termos dos atributos de outros símbolos gramaticais.

> [!info] Contexto
> Seção 4.3 de *Engineering a Compiler*. Formalismo para [[Análise Sensível ao Contexto]].

## Definição

- **Atributo**: valor associado a nós do parse tree
- Regras são **funcionais**: não implicam ordem de avaliação e definem cada atributo de forma única
- Cada instância de um símbolo gramatical no parse tree tem uma instância correspondente do atributo

## Tipos de Atributos

| Tipo | Fluxo | Definição |
|---|---|---|
| **Sintetizado** | filho → pai | definido em termos de atributos dos filhos |
| **Herdado** | pai → filho | definido em termos do pai ou irmãos |

Uma gramática **S-atribuída** usa apenas atributos sintetizados — avaliável em uma única passagem bottom-up.

## Exemplo: Números Binários com Sinal

```
Grammar SBN:
  Number → Sign List
  Sign   → + | -
  List   → List Bit | Bit
  Bit    → 0 | 1
```

Atributos:
- `Number.value`, `Sign.negative`, `List.value`, `List.position`, `Bit.value`

Regras de atribuição propagam `position` de cima para baixo (herdado) e `value` de baixo para cima (sintetizado).

## Métodos de Avaliação

| Método | Descrição |
|---|---|
| **Regra-a-regra (rule-based)** | ordem de avaliação derivada das dependências entre atributos |
| **Passagem em ordem** | treewalk simples (só funciona para S-atribuídas ou L-atribuídas) |
| **Preguiçoso (lazy)** | avalia atributo somente quando seu valor é requisitado |

### Grafo de Dependências
Para cada parse tree, constrói-se um grafo de dependências entre instâncias de atributos. A ordem topológica desse grafo define uma ordem válida de avaliação.

## Circularidade

Uma gramática é **circular** se existe alguma sentença cujo grafo de dependências contém um ciclo. Gramáticas circulares são problemáticas: um ciclo impede a avaliação.

Verificar circularidade é PSPACE-completo. Subclasses (S-atribuídas, L-atribuídas) são garantidamente acíclicas.

## Problemas com o Framework

- Regras de atribuição são numerosas: cada produção × atributo
- Difícil de especificar ações que dependem de contexto global
- Não se encaixa bem em algoritmos como register allocation ou seleção de modos de endereçamento

> [!warning] Limitação
> Otimizações que requerem múltiplas passagens ou contexto não-local são difíceis de expressar como gramáticas de atributos.

## Relações

- [[Sistemas de Tipos]] — gramáticas de atributos usadas para type checking
- [[Tradução Dirigida por Sintaxe — Ad Hoc]] — alternativa prática ao framework formal
- [[Parser — Análise Sintática]] — parse tree é o substrato da gramática de atributos
- [[Análise Sensível ao Contexto]] — contexto geral
