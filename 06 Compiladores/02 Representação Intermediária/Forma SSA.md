---
title: Forma SSA
tags:
  - compiladores
  - ir
  - otimização
aliases:
  - SSA
  - static single-assignment
  - static single-assignment form
  - forma de atribuição estática única
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Compiladores]]

# Forma SSA

**Static Single-Assignment Form**: convenção de naming onde **cada variável é definida exatamente uma vez** no texto do programa.

> [!info] Contexto
> Seção 5.4.2 de *Engineering a Compiler*. Extensão da [[IR Linear — Código de Três Endereços|IR linear]] que simplifica análise e otimização.

## Motivação

Em IR normal, uma variável pode ser definida em múltiplos pontos. Isso dificulta análises como:
- detecção de cópia redundante
- propagação de constante
- eliminação de subexpressão comum

SSA torna explícitas as relações definição → uso.

## Construção

Cada atribuição a uma variável `v` cria uma **nova versão**: `v₁`, `v₂`, `v₃`, ...

```
// IR normal:
x = a + b
x = x + c       // redefine x

// SSA:
x₁ = a + b
x₂ = x₁ + c
```

## Função φ (Phi)

Onde fluxos de controle se juntam (join points), a versão correta de uma variável pode vir de dois caminhos distintos. A **função φ** seleciona a versão correta:

```
// if-else gera join:
if (cond):
  x₁ = 1
else:
  x₂ = 2
x₃ = φ(x₁, x₂)   // x₃ = valor que chegou por qual caminho
```

φ não é uma instrução de máquina real; é removida antes da geração de código (por copy insertion ou register allocation).

## Propriedades

- Cada uso tem exatamente **uma definição alcançante** visível
- Simplifica análise de fluxo de dados
- Dominância do CFG define onde inserir funções φ (algoritmo de Cytron et al.)

## Algoritmo de Construção (Semipruned SSA)

### Fase 1 — Inserção de φ-functions

Usa [[Análise de Fluxo de Dados#Fronteiras de Dominância|fronteiras de dominância]] para determinar onde inserir φ.

**Nomes globais**: variável cujo uso pode vir de fora do bloco onde está definida (upward-exposed).

```
// Para cada nome global x:
WorkList ← Blocks(x)         // blocos com definição de x
while WorkList não vazio
    b ← remove de WorkList
    for d in DF(b)           // fronteira de dominância de b
        if d não tem φ para x
            inserir "x ← φ(x, x, ..., x)" no início de d
            WorkList ← WorkList ∪ {d}
```

### Fase 2 — Renomeação

Percorre a **dominator tree** em DFS, renomeando cada definição e uso de `x` com versão numerada:

```
Rename(b):
    for each φ-function "x ← φ(...)" in b
        rewrite x as NewName(x)      // cria nova versão x_i
    for each op "x ← y op z" in b
        rewrite y with top(stack[y])
        rewrite z with top(stack[z])
        rewrite x as NewName(x)
    for each successor s of b in CFG
        fill in φ-function params in s using top(stack[...])
    for each child c of b in dominator tree
        Rename(c)
    pop definitions introduced in b from stacks
```

## Variantes de SSA

| Variante | Critério para inserir φ | φ-functions mortas? |
|---|---|---|
| **Maximal SSA** | todo join point, toda variável | muitas |
| **Minimal SSA** | onde duas defs distintas se encontram | algumas |
| **Semipruned SSA** | apenas para nomes globais | poucas |
| **Pruned SSA** | semipruned + liveness | não |

Semipruned é o equilíbrio custo/precisão mais usado na prática.

## Vantagens para Otimização

| Otimização | Como SSA ajuda |
|---|---|
| Propagação de constante | definição única → fácil rastrear valor |
| Cópia-propagação | `x = y` implica x e y sempre iguais |
| Eliminação de código morto | variável sem usos = definição morta |
| Renomeação de registradores | versões distintas → variáveis naturalmente independentes |

## Modelos de Memória e SSA

SSA funciona bem para **variáveis escalares em registradores**. Para valores em memória (arrays, structs), SSA não se aplica diretamente — exige análise de aliasing separada.

> [!note] Modelo de Registradores
> Compiladores que geram IR com escalares em registradores (register-to-register model) se beneficiam mais de SSA do que os que usam modelo memória-a-memória.

## Relações

- [[IR Linear — Código de Três Endereços]] — SSA é extensão de three-address code
- [[IR Graphical — Árvores e Grafos]] — CFG define os join points onde inserir φ
- [[Análise de Fluxo de Dados]] — dominância e fronteiras de dominância guiam inserção de φ
- [[Otimizador de Compilador]] — SSA é a IR padrão de otimizadores modernos
- [[Back End do Compilador]] — SSA deve ser destruída antes da alocação de registradores
