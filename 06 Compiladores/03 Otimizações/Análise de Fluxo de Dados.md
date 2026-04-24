---
title: Análise de Fluxo de Dados
tags:
  - compiladores
  - otimização
  - análise
aliases:
  - data-flow analysis
  - análise de fluxo
  - iterative data-flow
  - dominância
  - dominance frontier
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Compiladores]]

# Análise de Fluxo de Dados

Framework de análise estática que raciocina sobre propriedades de um programa em tempo de compilação, propagando informações pelo [[IR Graphical — Árvores e Grafos|CFG]] até convergir num **ponto fixo**.

> [!info] Fonte
> *Engineering a Compiler*, Cooper & Torczon, cap. 9.

## Equações de Fluxo de Dados

Cada problema define equações sobre conjuntos associados a cada bloco `n`.

### Live-Variable Analysis

Variável `v` é **viva** (live) na saída de `n` se existe caminho de `n` até um uso de `v` sem passagem por uma redefinição.

**Conjuntos locais por bloco:**
- `UEVar(n)` — variáveis usadas em `n` antes de qualquer redefinição local (*upward-exposed*)
- `VarKill(n)` — variáveis definidas em `n`

**Equação global:**
$$
\text{LiveOut}(n) = \bigcup_{m \in \text{succ}(n)} \left(\text{UEVar}(m) \cup (\text{LiveOut}(m) \cap \overline{\text{VarKill}(m)})\right)
$$

Condição inicial: `LiveOut(n) = ∅` para todo `n`. Iteração até ponto fixo.

**Aplicações:**
- detectar variáveis não-inicializadas
- alocação de registradores
- construção de [[Forma SSA|SSA form]]

### Reaching Definitions

Definição `d` de `v` **alcança** ponto `p` se existe caminho de `d` até `p` sem redefinição intermediária de `v`.

Conjuntos: `DEDef(n)` (defs downward-exposed), `Kill(n)` (defs mortas por `n`).

## Algoritmo Iterativo

```
for each block b
    out[b] ← ∅          // initialise

changed ← true
while (changed)
    changed ← false
    for each block b in RPO order
        new_out ← f(b, in[b])   // transfer function
        if new_out ≠ out[b]
            out[b] ← new_out
            changed ← true
```

Convergência garantida para reticulados finitos e funções monótonas. Ordem RPO (reverse postorder) minimiza iterações.

## Análise de Dominância

Nó `a` **domina** `b` (a dom b) se todo caminho do nó inicial até `b` passa por `a`.

**IDom(b)** — dominador imediato: o mais próximo dominador de `b` diferente de `b`.

A estrutura IDom forma a **dominator tree** do CFG.

### Algoritmo Iterativo de Cooper

```
IDoms[b₀] ← b₀          // root domina si mesmo
for all other b: IDoms[b] ← Undefined
Changed ← true

while (Changed)
    Changed ← false
    for b in reverse_postorder (exceto b₀)
        NewIDom ← first processed predecessor of b
        for each other predecessor p of b
            if IDoms[p] ≠ Undefined
                NewIDom ← Intersect(p, NewIDom)
        if IDoms[b] ≠ NewIDom
            IDoms[b] ← NewIDom
            Changed ← true

Intersect(i, j):
    finger1 ← i; finger2 ← j
    while finger1 ≠ finger2
        while RPO(finger1) > RPO(finger2): finger1 ← IDoms[finger1]
        while RPO(finger2) > RPO(finger1): finger2 ← IDoms[finger2]
    return finger1
```

Complexidade: quase linear na prática via RPO.

## Fronteiras de Dominância

**DF(n)** (*dominance frontier*) = conjunto de blocos `y` tal que:
- `n` domina algum predecessor de `y`, **mas**
- `n` **não** domina estritamente `y`

Intuitivamente: onde a dominância de `n` termina.

**Uso crítico:** determina onde inserir funções φ na construção de [[Forma SSA|SSA form]].

```
DF(n) = { y | ∃ predecessor p de y tal que n dom p, mas n não dom_estrito y }
```

### Computation Algorithm

```
for each block n
    DF(n) ← ∅

for each block b with |pred(b)| ≥ 2
    for each predecessor p of b
        runner ← p
        while runner ≠ IDom(b)
            DF(runner) ← DF(runner) ∪ {b}
            runner ← IDom(runner)
```

## Variantes de Problemas Clássicos

| Problema | Direção | Operação join | Objetivo |
|---|---|---|---|
| Live variables | backward | union | registradores, SSA |
| Reaching definitions | forward | union | uso-definição |
| Available expressions | forward | intersection | CSE |
| Anticipable expressions | backward | intersection | code hoisting |

## Relações

- [[Forma SSA]] — fronteiras de dominância guiam inserção de φ-functions
- [[IR Graphical — Árvores e Grafos]] — CFG base do framework
- [[Otimizador de Compilador]] — análise alimenta transformações
- [[Back End do Compilador]] — liveness usada em alocação de registradores
