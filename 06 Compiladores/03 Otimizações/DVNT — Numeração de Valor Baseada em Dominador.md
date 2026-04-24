---
title: DVNT — Numeração de Valor Baseada em Dominador
tags:
  - compiladores
  - otimização
  - value-numbering
  - dominância
  - SSA
aliases:
  - DVNT
  - dominator-based value numbering
  - dominator value numbering
  - numeração de valor global
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Compiladores]]

# DVNT — Numeração de Valor Baseada em Dominador

Estende SVN (superlocal value numbering) para além de EBBs. Usa a **árvore de dominância** e o **espaço de nomes SSA** para detectar redundâncias globais.

Fonte: Engineering a Compiler, Cap. 10.5.2.

---

## Motivação

| Técnica | Escopo | Como detecta identidade de valor |
|---------|--------|----------------------------------|
| LVN | Bloco básico | Comparação léxica de nomes |
| SVN | EBB (extended basic block) | Comparação léxica por caminho |
| LCM | Procedimento (fluxo de dados) | Disponibilidade/antecipabilidade |
| **DVNT** | Procedimento (árvore de dom.) | **Nomes SSA como números de valor** |

### Problema do SVN

No CFG `B0 → B1 → {B2, B3} → B4`, SVN trata B4 sem contexto (nenhum predecessor na EBB). DVNT pode usar o conhecimento de B0 e B1 (dominadores de B4).

---

## Insight Central: Nomes SSA como Números de Valor

Em SSA, um nome usado em bloco `Bi` só pode vir de:
1. φ-function no topo de `Bi`, **ou**
2. Definição em bloco que domina `Bi`

Se nenhum bloco entre B1 e B4 redefine `x`, `x` tem o mesmo nome SSA em ambos → mesmo valor. A φ-function no topo de B4 marca explicitamente qualquer redefinição intermediária.

**Consequência**: usar o nome SSA diretamente como número de valor elimina o problema de rastrear redefinições intermediárias.

---

## Algoritmo DVNT

```
procedure DVNT(B):
  alocar novo escopo para B
  
  para cada φ-function "n ← φ(...)" em B:
    se p é meaningless (todos args iguais) ou redundant (φ idêntica já existe):
      VN[n] ← número de valor existente
      remover p
    senão:
      VN[n] ← n
      adicionar p à hash table
  
  para cada atribuição "x ← y op z" em B:
    sobrescrever y com VN[y], z com VN[z]
    buscar (op, VN[y], VN[z]) na hash table
    se encontrado (valor h):
      VN[x] ← h       -- redundante: reuso
    senão:
      VN[x] ← x
      inserir (op, VN[y], VN[z]) → x na hash table
  
  para cada filho c de B na árvore de dominância:
    DVNT(c)
  
  remover escopo de B da hash table
```

O escopo garante que entradas de B não poluam blocos não dominados por B (desempilhadas ao retornar).

---

## φ-functions: Meaningless vs. Redundant

- **Meaningless**: todos os argumentos têm o mesmo número de valor → φ é cópia trivial; remover
- **Redundant**: φ-function idêntica a outra já processada no mesmo bloco → substituir pelo resultado da outra

---

## Limitação vs. LCM

DVNT **não** propaga ao longo de **back edges** (arestas de loop). LCM pode encontrar redundâncias que DVNT não vê em código com loops.

> [!example]
> Expressão `a + b` computada no cabeçalho do loop: LCM pode movê-la para antes do loop; DVNT não detecta redundância entre iterações.

---

## Relações

- [[Forma SSA]] — DVNT depende fundamentalmente do espaço de nomes SSA
- [[Análise de Fluxo de Dados]] — dominância é derivada da análise de fluxo
- [[Lazy Code Motion (LCM)]] — complementar: LCM cobre back edges, DVNT é mais eficiente em casos sem loops
- [[Otimizador de Compilador]] — DVNT como pass de eliminação de redundância global
- [[Eliminação de Código Morto — Dead e Clean]] — operações redundantes removidas por DVNT podem tornar outras mortas
