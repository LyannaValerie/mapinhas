---
title: Eliminação de Código Morto — Dead e Clean
tags:
  - compiladores
  - otimização
  - dead-code
aliases:
  - Dead algorithm
  - Clean algorithm
  - eliminação de código inútil
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Compiladores]]

# Eliminação de Código Morto — Dead e Clean

Duas transformações complementares: **Dead** remove operações sem efeito externo; **Clean** simplifica o CFG resultante.

Fonte: Engineering a Compiler, Cap. 10.2.

---

## Dead — Eliminação de Código Inútil (Mark-Sweep em SSA)

Opera sobre [[Forma SSA]]. Classifica operações como *úteis* ou *inúteis* via análise de dependência de controle e uso.

### Fase 1 — Mark

1. Marcar todas as operações como **inúteis**
2. Marcar operações com efeito externo visível como **úteis** (I/O, stores em memória, retornos)
3. Para cada operação útil `x ← f(...)`:
   - Marcar cada operação que define os operandos de `x` como útil
   - Se `x` está em bloco `b` com dependência de controle não-trivial sobre bloco `b'`: marcar o branch final de `b'` como útil
4. Repetir até convergência (worklist)

### Fase 2 — Sweep

- Operações ainda marcadas como inúteis: remover ou substituir por `nop`
- Branches inúteis: substituir por jump ao pós-dominador imediato de `b'`

> [!note]
> A dependência de controle preserva branches necessários para execução correta mesmo que seu resultado não seja usado diretamente.

---

## Clean — Simplificação de CFG

Aplicado após Dead (ou outras transformações que esvaziam blocos). Quatro casos, processados em **pós-ordem**:

| Caso | Condição | Transformação |
|------|----------|---------------|
| **1 — Bloco vazio** | Bi tem só um sucessor Bj | Redirecionar arestas de entrada de Bi para Bj; remover Bi |
| **2 — Combinar blocos** | Bi tem único predecessor Bj com único sucessor | Fundir Bi em Bj |
| **3 — Içar branch** | Bi termina em jump para Bj vazio que termina em branch | Substituir jump de Bi pela cópia do branch de Bj |
| **4 — Blocos inatingíveis** | Bloco sem predecessores (exceto entrada) | Remover bloco |

Clean itera até que o CFG pare de mudar (back edges podem criar novas oportunidades).

> [!warning]
> Clean sozinho não elimina loop vazio. Cooperação Dead + Clean: Dead marca branch do loop vazio como inútil → Clean remove o bloco.

---

## Código Inatingível vs. Inútil

- **Inatingível**: nenhum caminho no CFG alcança o bloco → mark-sweep no CFG
- **Inútil/morto**: bloco alcançável, mas guardado por condição sempre falsa → requer análise de valores (ver [[SCCP — Propagação Esparsa de Constante Condicional]])

---

## Relações

- [[Otimizador de Compilador]] — contexto de passes de otimização
- [[Forma SSA]] — Dead opera sobre SSA; dependência de controle derivada do grafo de dominância
- [[Análise de Fluxo de Dados]] — liveness e controle de dependência usados no Mark
- [[DVNT — Numeração de Valor Baseada em Dominador]] — elimina redundâncias que podem tornar código morto
