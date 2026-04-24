---
title: SCCP — Propagação Esparsa de Constante Condicional
tags:
  - compiladores
  - otimização
  - SSA
  - dataflow
aliases:
  - SCCP
  - sparse conditional constant propagation
  - propagação de constante condicional
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Compiladores]]

# SCCP — Propagação Esparsa de Constante Condicional

Combina **propagação global de constantes** com **eliminação de código inatingível** em uma única passagem sobre [[Forma SSA|SSA]]. Autores: Wegman & Zadeck (1991). Fonte: Engineering a Compiler, Cap. 10.7.1.

Predecessor: SSCP (Reif & Lewis) — propagação simples sem consciência de desvios condicionais.

---

## O Problema que SCCP Resolve

Propagação simples (SSCP) processa **todas** as arestas do CFG, inclusive arestas nunca executadas. Isso polui o reticulado:

```
x = 5
if x > 3:          // condição sempre verdadeira
  y = x + 1        // ramo real:     y = 6
else
  y = x * 2        // ramo morto:    y = 10
// SSCP: meet(6, 10) = ⊥  →  y não é constante (ERRADO)
// SCCP: ignora ramo morto →  y = 6               (CORRETO)
```

SCCP rastreia quais arestas de controle são **executáveis** e propaga valores apenas por essas arestas.

---

## Reticulado de Valores

```
        ⊤  (undef — valor ainda desconhecido)
       /|\
      c1 c2 c3 ...  (valores constantes)
       \|/
        ⊥  (NAC — Not A Constant)
```

| Símbolo | Nome | Significado |
|---|---|---|
| ⊤ | top / undef | nó ainda não alcançado, ou valor indeterminado |
| c | constante | valor é exatamente c |
| ⊥ | bottom / NAC | valor não é constante (ou conflito) |

**Operação meet (∧):**

| ∧ | ⊤ | c | c' | ⊥ |
|---|---|---|---|---|
| **⊤** | ⊤ | c | c' | ⊥ |
| **c** | c | c | ⊥ | ⊥ |
| **c'** | c' | ⊥ | c' | ⊥ |
| **⊥** | ⊥ | ⊥ | ⊥ | ⊥ |

⊤ é neutro no meet: arestas não-executáveis contribuem ⊤ → não poluem o resultado.

---

## Estruturas do Algoritmo

| Estrutura | Conteúdo |
|---|---|
| `Value[n]` | reticulado atual de cada nome SSA n; inicia em ⊤ |
| `Executable[e]` | flag por aresta CFG e; inicia em **false** |
| `FlowWorkList` | arestas CFG pendentes de processamento |
| `SSAWorkList` | arestas SSA def→use pendentes de processamento |

---

## Algoritmo SCCP

### Inicialização

```
para todo nome SSA n:      Value[n] ← ⊤
para toda aresta CFG e:    Executable[e] ← false
FlowWorkList ← { aresta de entrada do procedimento }
SSAWorkList  ← ∅
```

### Loop principal

```
enquanto FlowWorkList ≠ ∅ ou SSAWorkList ≠ ∅:

  se FlowWorkList ≠ ∅:
    remover aresta (B→C) de FlowWorkList
    se Executable[B→C] = false:
      Executable[B→C] ← true
      Avaliar_φ(C)                         // φ-functions no topo de C
      se C tem exatamente 1 sucessor S:
        adicionar (C→S) a FlowWorkList
      senão:
        para cada op em C:
          Avaliar_op(op)

  se SSAWorkList ≠ ∅:
    remover aresta SSA (def x → uso em B) de SSAWorkList
    se alguma aresta entrada de B é executável:
      Avaliar_op(operação que usa x em B)
```

### Avaliar φ-function: `n ← φ(x1, x2, ..., xk)`

```
resultado ← ⊤
para cada (xi, aresta_i):
  se Executable[aresta_i]:
    resultado ← resultado ∧ Value[xi]
se resultado ≠ Value[n]:
  Value[n] ← resultado
  adicionar todas as arestas SSA (n → usos) a SSAWorkList
```

### Avaliar operação: `n ← f(x1, ..., xk)`

```
novo ← eval(f, Value[x1], ..., Value[xk])
  // se algum xi = ⊤: novo ← ⊤
  // se algum xi = ⊥: novo ← ⊥  (para a maioria das ops)
  // se todos são constantes: novo ← resultado concreto
se novo ≠ Value[n]:
  Value[n] ← novo
  adicionar arestas SSA (n → usos) a SSAWorkList
```

### Avaliar branch condicional: `cbr p → (L1, L2)`

```
se Value[p] = ⊤:    não adicionar nada (bloco ainda não alcançado)
se Value[p] = c:    adicionar apenas a aresta correspondente a c a FlowWorkList
se Value[p] = ⊥:    adicionar AMBAS as arestas a FlowWorkList
```

---

## Após o Algoritmo

1. **Substituir constantes**: toda operação onde todos os operandos têm Value = c → dobrar (`x = c`)
2. **Eliminar código inatingível**: blocos sem nenhuma aresta executável de entrada → remover
3. **Simplificar branches**: `cbr` com Value[p] = c → `jump` para o ramo constante
4. Aplicar [[Eliminação de Código Morto — Dead e Clean|Clean]] para compactar CFG resultante

---

## SCCP vs. SSCP

| Aspecto | SSCP | SCCP |
|---|---|---|
| Arestas processadas | todas | só executáveis |
| Desvios constantes | propaga pelos dois lados | propaga só pelo lado correto |
| Eliminação de código | não | sim (implícita) |
| Dependência | nenhuma | SSA form |
| Complexidade | O(N) | O(N) |

> [!example] Vantagem concreta
> Loop `for i = 1 to 10` com branch interno sempre-verdadeiro: SSCP não elimina o ramo falso; SCCP detecta que o ramo falso nunca é executável e propaga constantes corretamente ao longo do ramo verdadeiro.

---

## Complexidade

- Cada aresta CFG entra em `FlowWorkList` no máximo **uma vez** (flag `Executable`)
- Cada nome SSA pode ter seu valor movido **no máximo duas vezes** no reticulado (⊤→c, c→⊥)
- Logo: cada aresta SSA entra em `SSAWorkList` no máximo **duas vezes**
- Total: **O(N)** onde N = tamanho do programa em SSA

---

## Relações

- [[Forma SSA]] — SCCP depende de SSA; transmite valores por arestas def→use
- [[Análise de Fluxo de Dados]] — SCCP é dataflow esparso sobre reticulado
- [[Eliminação de Código Morto — Dead e Clean]] — SCCP expõe código inatingível; Clean compacta o CFG
- [[DVNT — Numeração de Valor Baseada em Dominador]] — outra análise baseada em SSA; complementar
- [[Otimizador de Compilador]] — SCCP como passe de otimização escalar global
- [[Lazy Code Motion (LCM)]] — LCM lida com redundância; SCCP com constantes e inatingibilidade
