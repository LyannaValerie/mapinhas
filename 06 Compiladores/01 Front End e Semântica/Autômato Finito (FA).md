---
title: Autômato Finito (FA)
tags:
  - compiladores
  - programação
  - teoria-da-computação
aliases:
  - FA
  - finite automaton
  - DFA
  - autômato finito determinístico
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Compiladores]]

# Autômato Finito (FA)

Modelo formal usado para implementar o [[Scanner — Análise Léxica|scanner]].

## Definição formal

$FA = (S, \Sigma, \delta, s_0, S_A)$

| Componente | Significado |
|---|---|
| $S$ | conjunto finito de estados |
| $\Sigma$ | alfabeto de entrada |
| $\delta: S \times \Sigma \to S$ | função de transição |
| $s_0$ | estado inicial |
| $S_A \subseteq S$ | estados de aceitação |

Estado especial $s_e$: estado de erro.

## Algoritmo de reconhecimento

```
char ← NextChar()
state ← s0
while (char ≠ eof and state ≠ se):
    state ← δ(state, char)
    char ← NextChar()

if state ∈ SA:
    report acceptance
else:
    report failure
```

## Diagrama de transição

Representação gráfica do FA.
- Estados = nós
- Transições = arestas rotuladas com caracteres
- Conjuntos finitos → diagramas acíclicos
- Conjuntos infinitos (inteiros, identificadores) → diagramas com **ciclos**

Ciclos exigem implementação com `while`, não com `if-then-else` aninhados.

## Exemplo: inteiros sem sinal

```
S = {s0, s1, s2, se}
Σ = {0..9}

δ:          0    1-9   outros
  s0   →   s1    s2    se
  s1   →   se    se    se
  s2   →   s2    s2    se
  se   →   se    se    se

SA = {s1, s2}
```

## Lexema vs categoria

- **Categoria**: classe do token (ex: `integer`)
- **Lexema**: texto concreto reconhecido pelo FA (ex: `113`)

## Relação com expressões regulares

Todo FA tem uma [[Expressões Regulares|expressão regular]] equivalente que descreve sua linguagem, e vice-versa.
Construção NFA→DFA (subset construction) converte RE em FA implementável.
