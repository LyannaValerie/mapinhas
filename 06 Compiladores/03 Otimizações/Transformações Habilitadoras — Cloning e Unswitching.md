---
title: Transformações Habilitadoras — Cloning e Unswitching
tags:
  - compiladores
  - otimização
  - enabling-transformations
  - cloning
aliases:
  - superblock cloning
  - procedure cloning
  - loop unswitching
  - transformações habilitadoras
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Compiladores]]

# Transformações Habilitadoras — Cloning e Unswitching

Transformações cujo objetivo principal é **criar oportunidades para outras otimizações**, não eliminar ineficiências diretamente.

Fonte: Engineering a Compiler, Cap. 10.6.

---

## 10.6.1 Superblock Cloning

### Motivação

Otimizações superlocais (baseadas em EBBs) são limitadas por **pontos de merge** no CFG onde caminhos se fundem e o contexto se perde.

### Técnica

1. Iniciar no cabeçalho do loop (loop head)
2. Clonar cada caminho até encontrar um back edge
3. Cada nó do corpo do loop passa a ter **único predecessor**

**Efeito**: todo caminho que a otimização encontra abrange exatamente uma iteração do loop. Para múltiplas iterações, combinar com loop unrolling.

### Benefícios

| Benefício | Mecanismo |
|-----------|-----------|
| Blocos mais longos | Mais contexto para LVN/SVN |
| Menos merges | Elimina φ-functions desnecessárias |
| Melhor scheduling | Mais liberdade para reordenar operações |

---

## 10.6.2 Procedure Cloning

Criar **múltiplas cópias do callee**, uma por contexto de chamada.

### Exemplo

```
P0 → P3 (param=1)
P1 → P3 (param=1)   →  P3a especializado para param=1
P2 → P3 (param=17)  →  P3b código geral
```

Propagação de constante interprocedural falha porque vê `1 ∧ 1 ∧ 17 = ⊥`. Com clonagem: P3a sempre recebe 1, compilador gera código otimizado para P3a.

> [!note]
> Alternativa a [[Especialização de Chamadas — Tail Call e Promoção#10.4.3 Promoção de Parâmetros|promoção de parâmetros]] com menos restrições — funciona mesmo sem prova de não-ambiguidade global.

---

## 10.6.3 Loop Unswitching

### Problema

If-then-else **invariante de loop** dentro do loop: branch executado em toda iteração sem necessidade.

```fortran
# Antes:
do i = 1 to n
  if (x > y)
    then a(i) = b(i) * x
    else a(i) = b(i) * y

# Depois (unswitched):
if (x > y)
  then do i = 1 to n; a(i) = b(i) * x
  else do i = 1 to n; a(i) = b(i) * y
```

### Por que LCM não resolve

LCM usa análise de fluxo sobre o CFG; mover código invariante de controle **modifica o próprio CFG** — a análise seria invalidada. Técnicas de value numbering reconhecem predicados idênticos mas não removem o branch do loop.

### Benefícios

- Loops com menos control flow → melhor scheduling e register allocation
- LCM consegue mover código invariante que estava dentro do if-then-else após unswitching

---

## 10.6.4 Renaming (Eliminação de False Sharing)

### False Sharing

Dois valores distintos compartilham o mesmo nome físico (registrador) por decisão do alocador, criando **restrições de dependência artificiais** no escalonador.

```
# Alocador coloca v1 e v2 no mesmo reg r3
# Scheduler vê dependência r3 → r3 onde não existe
```

### Solução

Renomear para separar os valores antes do scheduling:
- Inserir cópias / split lifetime para dar nomes distintos
- Equivalente à renomeação do registro na construção de SSA, mas em sentido inverso

> [!note]
> Trade-off: mais nomes = mais pressão por registradores. Renaming antes de scheduling, alocação depois.

---

## Relações

- [[Otimizador de Compilador]] — enabling transformations como passes na sequência de otimização
- [[Lazy Code Motion (LCM)]] — unswitching expõe código invariante que LCM pode então mover
- [[DVNT — Numeração de Valor Baseada em Dominador]] — superblock cloning cria blocos maiores que DVNT aproveita
- [[Especialização de Chamadas — Tail Call e Promoção]] — procedure cloning como alternativa à promoção
- [[Forma SSA]] — renaming relacionado à construção SSA
