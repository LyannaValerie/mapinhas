---
title: Sistemas de Tipos
tags:
  - compiladores
  - semântica
aliases:
  - type system
  - sistema de tipos
  - type checking
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Compiladores]]

# Sistemas de Tipos

**Tipo**: categoria abstrata que especifica propriedades compartilhadas por todos os seus membros.

Um sistema de tipos é o conjunto de tipos de uma linguagem de programação mais as regras que usam esses tipos para especificar o comportamento de programas válidos.

> [!info] Contexto
> Cap. 4 de *Engineering a Compiler*. Parte da [[Análise Sensível ao Contexto]].

## Propósitos do Sistema de Tipos

| Propósito | Descrição |
|---|---|
| **Segurança em tempo de execução** | detecta e evita erros antes da execução |
| **Expressividade** | permite sobrecarga de operadores e seleção de implementação correta |
| **Eficiência** | informação de tipo guia seleção de instruções e eliminação de checks desnecessários |

## Componentes de um Sistema de Tipos

### Tipos Base
Tipos atômicos predefinidos pela linguagem: `integer`, `real`, `boolean`, `character`.

### Tipos Construídos
Formados a partir de tipos base por construtores:
- **Produto** (`A × B`): struct/record — campos de tipos distintos em ordem
- **União** (`A ∪ B`): union — um valor de um dentre vários tipos
- **Mapeamento** (`A → B`): função/procedure — domínio A, contradomínio B
- **Potência** (`A^k`): array de k elementos de tipo A

### Regras de Tipo
Especificam operações válidas entre tipos. Ex.: FORTRAN 77 define conversões implícitas para aritmética mista.

## Type Checking vs Type Inference

| Mecanismo | Descrição |
|---|---|
| **Type checking** | verifica se usos de nomes são consistentes com declarações existentes |
| **Type inference** | deduz tipos de expressões sem declarações explícitas (ex.: APL, ML) |

- Linguagens com declarações: type checking estático é direto
- Sem declarações ou tipos variáveis em runtime: requer inferência iterativa sobre reticulado de tipos

## Equivalência de Tipos

- **Equivalência estrutural**: dois tipos são equivalentes se têm mesma estrutura
- **Equivalência por nome**: dois tipos são equivalentes apenas se têm o mesmo nome declarado
- C usa equivalência por nome para structs, estrutural para tipos base

## Coerção e Conversão

- **Coerção implícita** (type juggling): compilador insere conversão automaticamente
- **Cast/conversão explícita**: programador especifica conversão
- Coerções podem esconder erros; linguagens fortemente tipadas as limitam

## Relações

- [[Gramáticas de Atributos]] — formalismo para computação de tipos no parse tree
- [[Tradução Dirigida por Sintaxe — Ad Hoc]] — implementação prática de type checking
- [[Tabela de Símbolos]] — repositório central de informações de tipo por nome
- [[Análise Sensível ao Contexto]] — contexto geral do cap. 4
