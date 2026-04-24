---
title: Otimizador de Compilador
tags:
  - compiladores
  - programação
  - otimização
aliases:
  - optimizer
  - middle section
  - otimizador
  - compilador otimizador
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Compiladores]]

# Otimizador de Compilador

Fase intermediária do compilador. Transforma [[Representação Intermediária (IR)|IR]] em IR semanticamente equivalente, porém melhorada.

> [!warning] "Otimização" é um nome enganoso
> O otimizador raramente produz código *ótimo* — os problemas são NP-difíceis ou indecidíveis. Ele *melhora* o código em relação à versão não-otimizada.

## O que o otimizador faz

- **Analisa** o IR para descobrir fatos sobre o contexto de execução
- **Transforma** o IR reescrevendo operações para execução mais eficiente

Objetivos possíveis:
- reduzir tempo de execução (clássico)
- reduzir tamanho do código
- reduzir consumo de energia
- reduzir page faults

## Exemplo: code hoisting

```
# Antes
for i = 1 to n:
    a ← a × 2 × b × c × d   # 4 mults por iteração

# Depois (otimizador detecta que 2, b, c são invariantes no loop)
t ← 2 × b × c
for i = 1 to n:
    a ← a × d × t            # 2 mults por iteração (+ 2 fora)
```

Para `n > 1`, a versão otimizada é mais rápida.

## Tipos de análise usados

| Análise | Descrição |
|---|---|
| **Data-flow analysis** | raciocínio em tempo de compilação sobre fluxo de valores em runtime |
| **Dependence analysis** | testes numérico-teóricos sobre valores de expressões de subscritos (arrays) |

Data-flow: resolve sistema de equações de conjuntos derivado da estrutura do código.
Dependence: desambigua referências a elementos de array.

## Restrições de uma transformação

Para uma transformação ser válida, ela deve ser:

1. **Segura** (*safe*): preserva semântica em todos os casos. Ex: mover um store para fora de um loop só é seguro se o store não tem efeitos colaterais observáveis no loop.
2. **Lucrativa** (*profitable*): melhora o código. Nem toda transformação segura vale o custo (aumento de código, spill de registradores, etc).

## Escopo de otimização

| Escopo | Abrange | Exemplos |
|---|---|---|
| **Local** | bloco básico único | value numbering, constant folding |
| **Superlocal** | EBB (extended basic block) | superlocal VN |
| **Regional** | loop ou região | code motion, strength reduction |
| **Global** | procedimento inteiro | alocação de registradores global, inlining |
| **Interprocedural** | programa inteiro | análise de alias, whole-program opt |

Escopo maior → análise mais cara, ganhos maiores.

## Otimizações locais (bloco básico)

### Value Numbering

Detecta expressões com **mesmo valor** em tempo de compilação, mesmo que com nomes diferentes.

```
a ← x + y     → VN(a) = hash(+, VN(x), VN(y))
b ← x + y     → VN(b) = mesmo hash → substituir b por a
```

Captura: eliminação de subexpressão comum (CSE), propagação de cópia, constant folding, identidades algébricas (`x×1 = x`, `x+0 = x`).

### Dead Code Elimination

Remove definições cujo valor nunca é usado. Usa liveness: se `v ∉ LiveOut(b)` após a última def de `v` no bloco, a def é morta.

Ver algoritmo global: [[Eliminação de Código Morto — Dead e Clean]].

### Constant Folding

Avalia expressões com operandos constantes em tempo de compilação:
```
t ← 3 × 4   →   t ← 12
```

### Tree-Height Balancing

Rebalanceia árvores de expressões associativas e comutativas para **reduzir profundidade crítica** e maximizar paralelismo de instruções:

```
# Antes (sequencial, altura 3):
t1 ← a + b
t2 ← t1 + c
t3 ← t2 + d

# Depois (balanceado, altura 2):
t1 ← a + b
t2 ← c + d
t3 ← t1 + t2
```

## Otimizações globais

### Code Motion (Hoisting)

Move computação **invariante de loop** para antes do loop:

```
# Antes
for i = 1 to n:
    a ← a × 2 × b × c × d   # 2, b, c invariantes

# Depois
t ← 2 × b × c
for i = 1 to n:
    a ← a × d × t
```

Seguro se a computação não tem efeitos colaterais e não é eliminada em algum caminho onde antes era.

Ver algoritmo completo (PRE + redundância parcial): [[Lazy Code Motion (LCM)]].

### Strength Reduction

Substitui operação cara por equivalente mais barata:
```
i × 4   →   shift left 2
i²      →   acumulação incremental dentro do loop
```

Ver algoritmo sobre SSA: [[OSR — Redução de Força por Operador]].

### Inline Substitution

Substitui chamada de função pelo corpo da função, eliminando overhead de call/return e expondo mais contexto para otimizações locais.

## Estrutura interna

- Pode ser **monolítica** (um pass, várias otimizações) ou **multipass** (cada pass lê/escreve IR)
- O número e propósito dos passes variam por compilador

Ver [[Compilador — Estrutura de Fases]].

## Relações

- [[Análise de Fluxo de Dados]] — análise que alimenta as transformações
- [[Forma SSA]] — IR canônica para otimização; simplifica value numbering e propagação
- [[IR Linear — Código de Três Endereços]] — IR típica de entrada/saída do otimizador
- [[Back End do Compilador]] — recebe IR otimizada para geração de código
- [[Eliminação de Código Morto — Dead e Clean]] — Dead (mark-sweep SSA) + Clean (CFG)
- [[Lazy Code Motion (LCM)]] — PRE + code motion com 4 análises de fluxo
- [[DVNT — Numeração de Valor Baseada em Dominador]] — value numbering global via árvore de dominância
- [[Especialização de Chamadas — Tail Call e Promoção]] — tail call, leaf call, promoção de parâmetros
- [[Transformações Habilitadoras — Cloning e Unswitching]] — superblock/procedure cloning, unswitching, renaming
- [[OSR — Redução de Força por Operador]] — strength reduction sobre grafo SSA + LFTR
