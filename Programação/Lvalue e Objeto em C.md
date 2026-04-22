---
title: Lvalue e Objeto em C
aliases:
  - lvalue C
  - rvalue C
  - objeto C
  - lvalue rvalue
tags:
  - computação/programação/c
date: 2026-04-21
---

# Lvalue e Objeto em C

## Definição

**Objeto**: região nomeada de armazenamento (memória com identidade).
**Lvalue**: expressão que se refere a um objeto. O nome vem de "left value" — o que pode aparecer à esquerda de uma atribuição (`E1 = E2`).

Fonte: K&R Appendix A, §A.5.

## Relações (SPO)

| Sujeito | Predicado | Objeto |
|---|---|---|
| lvalue | refere-se a | objeto em memória |
| identificador | é | lvalue (se tipo e classe de armazenamento adequados) |
| `*E` | é lvalue se | E é ponteiro |
| operador de atribuição | exige | lvalue no operando esquerdo |
| operador | pode ou não | produzir lvalue como resultado |

## Exemplos

```c
int x = 10;
x = 5;        /* x é lvalue: refere-se a objeto nomeado */

int *p = &x;
*p = 7;       /* *p é lvalue: desreferência de ponteiro */

int a[3];
a[1] = 4;     /* a[1] é lvalue */

/* NÃO são lvalues: */
5 = x;        /* constante não é lvalue — erro de compilação */
(x + 1) = 5; /* expressão aritmética não é lvalue */
```

## Lvalue modificável

Nem todo lvalue pode ser atribuído. Lvalue **não modificável**:
- objeto com qualificador `const`
- array (nome do array não é lvalue modificável)
- struct/union com campo `const`

```c
const int C = 1;
C = 2;          /* erro: lvalue não modificável */

int arr[3];
arr = ...;      /* erro: nome de array não é lvalue modificável */
```

## Contextos que exigem lvalue

- Operando esquerdo de `=`, `+=`, `-=`, etc.
- Operandos de `++` e `--` (pré e pós-fixo)
- Argumento de `&` (endereço de)

> [!info] Rvalue
> Expressão que não é lvalue — usada apenas para ler seu valor, não para endereçar. Ex: `x + 1`, `5`, valor de retorno de função.

## Ver também

- [[Ponteiros em C]] — desreferência cria lvalue
- [[Qualificadores de Tipo em C]] — const cria lvalue não-modificável
- [[Operadores C]] — operadores que produzem lvalue
- [[Arrays em C]] — nome de array como endereço
