---
title: Typedef em C
aliases:
  - typedef C
  - alias de tipo C
tags:
  - computação/fundamentos
date: 2026-04-21
---

# Typedef em C

## Definição

`typedef` cria um novo **nome** para um tipo existente — não cria novo tipo. Sintaxe: nome aparece na posição de variável.

## Relações (SPO)
- `typedef` → adiciona → alias para tipo existente
- `typedef` → não cria → novo tipo (sem semântica nova)
- `typedef` → difere de `#define` → compilador processa (pode lidar com tipos complexos)

---

## Sintaxe

```c
typedef int Length;            /* Length é alias para int */
typedef char *String;          /* String é alias para char* */
typedef int (*PFI)(char *, char *);  /* PFI: ponteiro para função */
```

Nome do tipo novo fica na **posição de variável**:

```c
Length len, maxlen;
String p, lineptr[MAXLINES], alloc(int);
int strcmp(String, String);
p = (String) malloc(100);
PFI strcmp, numcmp;            /* ponteiros para função */
```

---

## Para structs

```c
typedef struct tnode *Treeptr;
typedef struct tnode {
    char *word;
    int count;
    struct tnode *left;
    struct tnode *right;
} Treenode;

Treeptr talloc(void)
{
    return (Treeptr) malloc(sizeof(Treenode));
}
```

`Treenode` e `Treeptr` são mais legíveis que `struct tnode` e `struct tnode *`.

---

## Por que usar typedef

1. **Portabilidade**: tipos dependentes de máquina ficam isolados. Para mudar de `int` para `long`, muda só o `typedef`. Exemplos da stdlib: `size_t`, `ptrdiff_t`.

2. **Documentação**: `Treeptr` comunica intenção melhor que `struct tnode *`.

> [!info]
> `typedef` é sintaticamente como classe de armazenamento (`extern`, `static`). Convenção: nomes typedef em maiúscula para destacar.

---

## Ver também
- [[Structs em C]] — uso principal de typedef
- [[Ponteiros em C]] — `typedef` para ponteiros de função
- [[Pré-processador C]] — `#define` como alternativa mais limitada
