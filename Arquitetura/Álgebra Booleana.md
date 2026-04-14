---
title: Álgebra Booleana
aliases:
  - Boolean algebra
  - álgebra de Boole
  - De Morgan
  - soma de produtos
  - SOP
  - minterms
  - mapa de Karnaugh
tags:
  - computação/arquitetura
  - computação/fundamentos
date: 2026-04-12
source: "Organização Estruturada de Computadores — Tanenbaum, Cap. 3"
---

# Álgebra Booleana

Sistema algébrico com dois valores (0 e 1) que fundamenta o projeto de circuitos digitais. Proposto por George Boole (1854); aplicado a circuitos por Claude Shannon (1938).

→ Implementação física: [[Portas Lógicas]]  
→ Circuitos resultantes: [[Circuitos Combinatórios]]

---

## Relações (SPO)

- Álgebra Booleana → governa → comportamento de [[Portas Lógicas]]
- Teoremas de De Morgan → permitem → substituição AND↔OR com inversão
- Mapa de Karnaugh → simplifica → expressões booleanas por agrupamento visual

---

## Postulados e Identidades Fundamentais

| Identidade AND | Identidade OR |
|---|---|
| A · 0 = 0 | A + 1 = 1 |
| A · 1 = A | A + 0 = A |
| A · A = A | A + A = A |
| A · Ā = 0 | A + Ā = 1 |
| A · B = B · A | A + B = B + A |

### Princípio da Dualidade

Toda identidade booleana tem uma **dual**: troca AND ↔ OR e 0 ↔ 1.  
Se uma identidade é verdadeira, sua dual também é.

---

## Propriedades

### Comutatividade
```
A · B = B · A
A + B = B + A
```

### Associatividade
```
(A · B) · C = A · (B · C)
(A + B) + C = A + (B + C)
```

### Distributividade
```
A · (B + C) = (A · B) + (A · C)
A + (B · C) = (A + B) · (A + C)
```

### Absorção
```
A · (A + B) = A
A + (A · B) = A
```

### Complemento Duplo
```
NOT(NOT A) = A
```

---

## Teoremas de De Morgan

> [!important] De Morgan
> $$\overline{A \cdot B} = \bar{A} + \bar{B}$$
> $$\overline{A + B} = \bar{A} \cdot \bar{B}$$

**Em palavras:**
- NOT de um AND = OR dos complementos
- NOT de um OR = AND dos complementos

**Aplicação prática:** converter circuito AND → equivalente NOR; converter OR → equivalente NAND. Permite projetar qualquer circuito usando apenas portas NAND ou apenas NOR (ver [[Portas Lógicas#Universalidade de NAND e NOR]]).

**Generalização para n variáveis:**
$$\overline{A_1 \cdot A_2 \cdots A_n} = \bar{A}_1 + \bar{A}_2 + \cdots + \bar{A}_n$$
$$\overline{A_1 + A_2 + \cdots + A_n} = \bar{A}_1 \cdot \bar{A}_2 \cdots \bar{A}_n$$

---

## Forma Canônica: Soma de Produtos (SOP)

Qualquer função booleana pode ser escrita como uma **soma (OR) de produtos (AND)** — também chamada de **soma de minterms**.

### Procedimento
1. Identificar todas as linhas da tabela-verdade onde a saída = 1
2. Para cada linha, escrever um **minterm**: AND de todas as variáveis (complementadas se = 0)
3. Fazer OR de todos os minterms

**Exemplo** — função f(A,B,C) = 1 para (0,1,1), (1,0,1), (1,1,0), (1,1,1):

| A | B | C | f |
|---|---|---|---|
| 0 | 0 | 0 | 0 |
| 0 | 0 | 1 | 0 |
| 0 | 1 | 0 | 0 |
| 0 | 1 | 1 | 1 |
| 1 | 0 | 0 | 0 |
| 1 | 0 | 1 | 1 |
| 1 | 1 | 0 | 1 |
| 1 | 1 | 1 | 1 |

$$f = \bar{A}BC + A\bar{B}C + AB\bar{C} + ABC$$

Pode ser simplificada com álgebra ou Mapa de Karnaugh.

---

## Mapa de Karnaugh (K-map)

Ferramenta visual para minimizar expressões booleanas. Disposição especial onde células adjacentes diferem em **uma única variável** (código Gray).

### Mapa 2 variáveis (4 células)

```
      B=0  B=1
A=0 |  f  |  f  |
A=1 |  f  |  f  |
```

### Mapa 4 variáveis (16 células)

```
       CD=00  CD=01  CD=11  CD=10
AB=00 |      |      |      |      |
AB=01 |      |      |      |      |
AB=11 |      |      |      |      |
AB=10 |      |      |      |      |
```

### Regras de agrupamento
- Agrupar apenas **1s** em retângulos de tamanho 2ⁿ (1, 2, 4, 8…)
- Grupos maiores = expressão simplificada com menos termos
- Adjacência inclui bordas opostas (o mapa é toroidal)
- Cada 1 deve pertencer a pelo menos um grupo

> [!tip] Minimização
> Cada grupo de tamanho 2ⁿ elimina n variáveis da expressão. Um grupo de 4 em 4 variáveis → termo com 2 variáveis. Um grupo de 8 → termo com 1 variável.

---

## Ver também

- [[Portas Lógicas]] — AND, OR, NOT, NAND, NOR, XOR implementam operações booleanas
- [[Circuitos Combinatórios]] — síntese de circuitos a partir de expressões booleanas
- [[Dados Binários]] — sistema binário que sustenta os dois valores (0, 1)
