---
title: Tradução Dirigida por Sintaxe — Ad Hoc
tags:
  - compiladores
  - semântica
aliases:
  - ad hoc syntax-directed translation
  - SDT
  - tradução dirigida por sintaxe
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Compiladores]]

# Tradução Dirigida por Sintaxe — Ad Hoc

Abordagem prática para análise sensível ao contexto: o compilador associa **snippets de código** diretamente a produções da gramática. Cada snippet é invocado quando o parser reconhece a produção correspondente.

> [!info] Contexto
> Seção 4.4 de *Engineering a Compiler*. Alternativa prática às [[Gramáticas de Atributos]].

## Mecanismo

- Em parsers **top-down (recursive descent)**: código inserido diretamente nas rotinas de parsing
- Em parsers **bottom-up (shift-reduce)**: ações executadas a cada operação *reduce*

Cada ação é associada ao lado direito de uma produção específica. Quando o parser reduz pela produção, a ação correspondente é invocada.

## Notação Yacc

Adotada por muitos geradores de parsers (Yacc, Bison, etc.):

| Símbolo | Significado |
|---|---|
| `$$` | valor do símbolo no lado esquerdo da produção |
| `$1`, `$2`, ... | valores dos símbolos no lado direito, em ordem |

### Exemplo — Números Binários com Sinal

```
Number → Sign List     { $$ = $1 * $2; }
Sign   → '+'           { $$ = 1; }
Sign   → '-'           { $$ = -1; }
List   → Bit           { $$ = $1; }
List0  → List1 Bit     { $$ = 2 * $1 + $2; }
Bit    → '0'           { $$ = 0; }
Bit    → '1'           { $$ = 1; }
```

## Comunicação entre Ações

O parser integra o armazenamento de valores à sua própria pilha interna (stack). Para cada símbolo gramatical na pilha, há um slot correspondente para o valor semântico.

Simplificações em relação às gramáticas de atributos:
- **Valores fluem em uma direção**: folhas → raiz (apenas atributos sintetizados)
- **Um único valor por símbolo gramatical**

## Implementação

```
// Esqueleto do parser LR com ações
reduce(production p):
  execute action for p
  pop |rhs(p)| symbols from stack
  push lhs(p) with computed value
```

## Herança de Valores

Para propagar informação de pai para filho (atributos herdados) em parsers bottom-up, usa-se **marker nonterminals**: não-terminais inseridos artificialmente que "lembram" o contexto antes de o parser continuar.

> [!warning] Cuidado
> Marker nonterminals podem introduzir conflitos shift/reduce na gramática.

## Mudança de Associatividade

SDT ad hoc pode implementar gramáticas com associatividade direita reescrevendo as produções, mesmo que o parser seja LALR(1) e a gramática original tivesse conflitos.

## Relações

- [[Gramáticas de Atributos]] — formalismo formal do qual o SDT ad hoc é simplificação
- [[Parser — Análise Sintática]] — parser executa as ações SDT
- [[Parsing LL(1) e LR(1)]] — parsers LR são os mais comuns para SDT bottom-up
- [[Tabela de Símbolos]] — tipicamente construída via ações SDT
- [[Sistemas de Tipos]] — type checking implementado como ações SDT
