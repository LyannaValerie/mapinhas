---
title: OSR — Redução de Força por Operador
tags:
  - compiladores
  - otimização
  - strength-reduction
  - induction-variable
  - SSA
aliases:
  - OSR
  - operator strength reduction
  - LFTR
  - linear-function test replacement
  - redução de força
  - variável de indução
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Compiladores]]

# OSR — Redução de Força por Operador

Substitui multiplicações dentro de loops por adições incrementais equivalentes, eliminando computações de endereço baseadas em índice de loop.

Fonte: Engineering a Compiler, Cap. 10.7.2.

---

## Conceitos Fundamentais

**Variável de indução (IV)**: valor que aumenta ou diminui por quantidade constante a cada iteração do loop.

**Constante de região**: valor invariante dentro de um dado loop (sua definição domina o cabeçalho do loop).

**Operação candidata**: `x ← i × c` ou `x ← i + c` onde `i` é IV e `c` é constante de região.

---

## Motivação

```
sum ← 0
for i ← 1 to 100
  sum ← sum + a(i)
```

Endereço de `a(i)` expandido: `(i-1) × 4 + @a` → 4 operações por iteração.

Sequências de valores: `r1 = {0,1,...,99}`, `r2 = {0,4,...,396}`, `r3 = {@a, @a+4, ..., @a+396}`.

OSR detecta que `r3` é IV com passo 4 e valor inicial `@a`. Substitui multiplicação por adição incremental.

Resultado: loop passa de 8 operações para 5 (sem φ-functions), e cria forma adequada para modo de endereçamento autoincremento.

---

## Algoritmo OSR

Opera sobre o **grafo SSA** (nós = operações SSA, arestas = uso de valores).

### Estrutura de dados por nó

- `Num`: número DFS
- `Low`: menor Num alcançável (Tarjan SCC)
- `Header`: aponta para cabeçalho do SCC (nó com menor RPO no SCC)

### Fluxo principal: DFS + Tarjan SCCs

```
OSR:
  para cada nó n não visitado no grafo SSA:
    DFS(n)

Process(SCC N):
  se N tem um único nó n:
    se n é candidato: Replace(n, iv, rc)
    senão: n.Header ← null
  senão:
    ClassifyIV(N)

ClassifyIV(N):
  se todos os nós de N são atualizações válidas de IV:
    header ← nó de N com menor RPO
    para cada n em N: n.Header ← header
  senão:
    para cada n em N:
      se n é candidato: Replace(n, iv, rc)
      senão: n.Header ← null
```

### Replace e Reduce

- `Replace(n, iv, rc)`: verifica hash table para par (iv, rc); se existe → cópia; senão → `Clone(iv)` + `Reduce`
- `Reduce`: percorre SCC de `iv` clonando cada nó para criar nova IV; para operandos externos → chama `Apply`
- `Apply(opcode, o1, o2)`: insere nova operação no ponto correto (dominado pelos definidores de o1 e o2)

> [!note]
> Para candidato que é multiplicação: `Reduce` ajusta o incremento como produto do incremento original pelo `rc`.

---

## LFTR — Linear-Function Test Replacement

Após OSR, a variável de indução original pode sobrar apenas no **teste de fim de loop**. LFTR reescreve o teste para usar a nova IV reduzida.

### Passos

1. Localizar comparações que usam IVs mortas (só existem por causa do teste)
2. Localizar IV substituta (criada por OSR)
3. Computar nova constante de região para o teste reescrito
4. Reescrever o código

### Arestas LFTR

OSR insere **arestas LFTR rotuladas** da IV original para cada IV reduzida derivada, com label = (operação, constante de região) usada na redução.

```
# Exemplo: ri → r1 (label: -1) → r2 (label: ×4) → r3 (label: +@a)
# Teste original: ri ≤ 100
# Teste reescrito: r3 ≤ (100 - 1) × 4 + @a = 396 + @a
```

LFTR percorre a cadeia de arestas para encontrar a constante de região correta para o teste reescrito.

---

## Resultado Combinado

OSR + LFTR + Dead:
- Elimina IV original e todas as IVs intermediárias como código morto
- Loop com apenas adição incremental e teste reescrito
- Habilita modo de endereçamento autoincremento no gerador de código

---

## Relações

- [[Forma SSA]] — OSR opera inteiramente sobre grafo SSA
- [[Otimizador de Compilador]] — OSR como pass regional de otimização
- [[Eliminação de Código Morto — Dead e Clean]] — Dead remove IVs mortas após OSR+LFTR
- [[Transformações Habilitadoras — Cloning e Unswitching]] — superblock cloning cria contexto para OSR
- [[Análise de Fluxo de Dados]] — dominância necessária para Apply inserir operações no lugar correto
