---
title: Structs em C
aliases:
  - struct C
  - estrutura C
  - registro C
  - struct pointer C
  - árvore binária C
tags:
  - computação/fundamentos
date: 2026-04-21
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Programação]]

# Structs em C

## Definição

Coleção de uma ou mais variáveis, possivelmente de tipos diferentes, agrupadas sob um nome. Permite tratar dados relacionados como unidade. Equivalente a "record" (Pascal) ou "struct" (Go/Rust).

## Relações (SPO)
- `struct` → agrupa → variáveis de tipos distintos
- Membro de struct → acessado por → operador `.` (valor) ou `->` (ponteiro)
- Struct → pode conter → outras structs (aninhamento)
- Struct auto-referencial → contém → ponteiro para struct do mesmo tipo (listas, árvores)
- `sizeof(struct)` → pode ser > → soma dos membros (alinhamento/padding)

---

## 6.1 Declaração básica

```c
struct point {
    int x;
    int y;
};

struct point pt;               /* variável do tipo struct point */
struct point pt = {320, 200};  /* inicialização */
```

- Tag (`point`) nomeia o tipo; usada depois como `struct point`
- Membros e variáveis comuns podem ter o mesmo nome sem conflito
- `struct { ... } x, y, z;` — declara e define variáveis inline (sem tag reutilizável)

### Acesso a membros

```c
printf("%d,%d", pt.x, pt.y);         /* operador . */
double dist = sqrt((double)pt.x * pt.x + (double)pt.y * pt.y);
```

### Aninhamento

```c
struct rect {
    struct point pt1;
    struct point pt2;
};

struct rect screen;
screen.pt1.x    /* coordenada x do canto superior esquerdo */
```

---

## 6.2 Structs e funções

Operações legais em structs: copiar, atribuir, tirar endereço (`&`), acessar membros. **Não** comparar com `==`.

```c
/* retorna struct por valor */
struct point makepoint(int x, int y)
{
    struct point temp;
    temp.x = x;
    temp.y = y;
    return temp;
}

screen.pt1 = makepoint(0, 0);
screen.pt2 = makepoint(XMAX, YMAX);
```

Para funções que modificam struct, passar **ponteiro**:

```c
/* addpoint: soma dois pontos */
struct point addpoint(struct point p, struct point q)
{
    p.x += q.x;
    p.y += q.y;
    return p;
}
```

---

## 6.4 Ponteiros para struct e operador `->`

```c
struct point *pp = &pt;

pp->x    /* equivale a (*pp).x */
pp->y    /* equivale a (*pp).y */
```

`->` tem precedência mais alta que `*` e `++`.

### Arrays de structs e aritmética de ponteiros

```c
struct key keytab[] = { ... };
struct key *p;

for (p = keytab; p < keytab + NKEYS; p++)
    printf("%4d %s\n", p->count, p->word);
```

`p++` avança pelo tamanho completo da struct.

> [!warning] sizeof de struct
> `sizeof(struct { char c; int i; })` pode retornar 8, não 5 — padding por alinhamento. Usar `sizeof` ao invés de calcular manualmente.

---

## 6.5 Structs auto-referenciais (árvore binária)

Um nó pode conter ponteiros para nós do mesmo tipo:

```c
struct tnode {
    char *word;           /* texto */
    int count;            /* ocorrências */
    struct tnode *left;   /* subárvore esquerda (< word) */
    struct tnode *right;  /* subárvore direita (>= word) */
};
```

Inserção recursiva:

```c
struct tnode *addtree(struct tnode *p, char *w)
{
    int cond;
    if (p == NULL) {        /* nova palavra */
        p = talloc();
        p->word = strdup(w);
        p->count = 1;
        p->left = p->right = NULL;
    } else if ((cond = strcmp(w, p->word)) == 0)
        p->count++;
    else if (cond < 0)
        p->left = addtree(p->left, w);
    else
        p->right = addtree(p->right, w);
    return p;
}
```

Mesmo padrão para listas encadeadas: `struct node { ...; struct node *next; }`.

---

## Ver também
- [[Ponteiros em C]] — ponteiros, `->`, aritmética
- [[Recursão em C]] — funções recursivas em árvores
- [[Typedef em C]] — aliases para tipos struct
- [[Unions em C]] — variante de struct com sobreposição de memória
- [[Inicialização em C]] — inicialização de struct
