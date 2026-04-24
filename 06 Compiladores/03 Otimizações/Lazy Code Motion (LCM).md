---
title: Lazy Code Motion (LCM)
tags:
  - compiladores
  - otimização
  - code-motion
  - redundância
aliases:
  - LCM
  - lazy code motion
  - partial redundancy elimination
  - PRE
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Compiladores]]

# Lazy Code Motion (LCM)

Combina **eliminação de redundância parcial** com **movimentação de código invariante de loop**. Move expressões para pontos de execução menos frequente. Usa quatro problemas de fluxo de dados encadeados.

Fonte: Engineering a Compiler, Cap. 10.3.1.

---

## Motivação

- **Redundância total**: expressão recomputada em todo caminho → substituir por reuso
- **Redundância parcial**: expressão computada em *alguns* caminhos, não todos → inserir em caminhos faltantes, transformar em redundância total, remover

LCM resolve ambos os casos com *colocação mínima e o mais tarde possível* (*lazy*).

---

## Análises de Fluxo de Dados

### 1 — Disponibilidade (Availability) — Forward

`e ∈ AvailIn(j)` se toda expressão `e` foi computada em **todo** caminho da entrada até `j` e nenhum operando foi redefinido.

### 2 — Antecipabilidade (Anticipability) — Backward

`e ∈ AntIn(b)` se `e` será avaliada ao longo de **todo** caminho que sai de `b` antes de qualquer redefinição de operando.

Usada também em **Code Hoisting** (ver abaixo).

### 3 — Earliest(i, j) — por aresta

Ponto mais cedo onde a expressão pode ser colocada sem perder nenhuma antecipação:

```
Earliest(i,j) = AntIn(j) ∩ ¬AvailOut(i) ∩ (¬AntOut(i) ∪ DEExpr(i))
```

### 4 — Later / LaterIn — Forward

Quanto a colocação pode ser adiada além do Earliest sem perder benefício:

```
LaterIn(j) = ∩ Later(i, j)   para i ∈ pred(j)
Later(i, j) = Earliest(i,j) ∪ (LaterIn(i) ∩ ¬UEExpr(i))
```

---

## Reescrita do Código

```
Insert(i,j) = Later(i,j) ∩ ¬LaterIn(j)   -- inserir na aresta (i,j)
Delete(i)   = UEExpr(i) ∩ LaterIn(i)      -- remover primeira avaliação em i
```

> [!warning]
> Se a aresta `(i,j)` é **crítica** (i tem múltiplos sucessores e j múltiplos predecessores): dividir a aresta inserindo bloco intermediário antes de inserir `Insert(i,j)`.

---

## Exemplo de Fluxo

```
B1: cmp LT r1,r2 → cbr → B2a, B3
B2a: mult r17,r18 ⇒ r20; add r19,r20 ⇒ r21; jump → B2
B2: loop body...
B3: ...
```

LCM move `mult` e `add` para `B2a` (inserção na aresta B1→B2a), elimina cópias redundantes em B2.

---

## Code Hoisting

Variante para **redução de tamanho de código** (não frequência).

Se `e ∈ AntOut(b)`: avaliar `e` no fim de `b` e substituir primeira ocorrência em cada caminho saindo de `b`. Reduz número de cópias; usa antecipabilidade diretamente.

Simétrico: **code sinking** (cross jumping) — mover código comum de vários predecessores para o sucessor.

---

## Propriedades

- Colocação mínima de inserções (lazy = o mais tarde possível)
- Não aumenta o comprimento do caminho mais longo
- Subsume disponibilidade clássica (AE) e antecipabilidade

---

## Relações

- [[Análise de Fluxo de Dados]] — todos os problemas de fluxo usados aqui
- [[Otimizador de Compilador]] — LCM como pass global de otimização
- [[DVNT — Numeração de Valor Baseada em Dominador]] — alternativa SSA-based para redundância; LCM propaga em back edges, DVNT não
- [[Eliminação de Código Morto — Dead e Clean]] — Dead + Clean removem operações que LCM torna mortas
