---
title: Ponteiros em C
aliases:
  - pointer C
  - ponteiro C
  - endereço C
  - derreferenciamento C
  - aritmética de ponteiros
  - ponteiro para função C
tags:
  - computação/fundamentos
date: 2026-04-21
---

# Ponteiros em C

## Definição

Variável que contém o **endereço** de outra variável. Permite acesso indireto a objetos em memória. Ponteiro tem tipo: `int *`, `char *`, `double *` — determina escala da aritmética.

## Relações (SPO)
- Operador `&` → retorna → endereço de objeto
- Operador `*` → acessa → objeto apontado (derreferenciamento)
- Ponteiro `+n` → avança → n objetos do tipo apontado
- Nome de array → é → endereço do elemento 0 (constante, não variável)
- Ponteiro nulo (0 / NULL) → nunca aponta para → dado válido

---

## 5.1 Ponteiros e endereços

```c
int x = 1, y = 2, z[10];
int *ip;              /* ip é ponteiro para int */

ip = &x;              /* ip aponta para x */
y = *ip;              /* y = 1 (valor de x) */
*ip = 0;              /* x = 0 */
ip = &z[0];           /* ip aponta para z[0] */
```

- `&` aplica somente a objetos em memória (variáveis, elementos de array)
- `*` e `&` agrupam da direita para esquerda: `(*ip)++` incrementa o objeto, `*ip++` incrementa o ponteiro
- `int *ip` — mnemônico: `*ip` é `int`

```c
double *dp, atof(char *);   /* *dp é double; atof recebe char* e retorna double */
```

---

## 5.2 Ponteiros como argumentos de função

Call by value não permite que função altere variável do chamador. Solução: passar ponteiro.

```c
void swap(int *px, int *py)
{
    int temp;
    temp = *px;
    *px = *py;
    *py = temp;
}

swap(&a, &b);   /* passa endereços */
```

Padrão usado por `scanf`: ponteiro argumento recebe valor convertido de volta ao chamador.

---

## 5.3 Ponteiros e arrays

Equivalência fundamental em C:

```c
int a[10];
int *pa = a;    /* pa aponta para a[0]; equivale a &a[0] */

x = *pa;        /* = a[0] */
x = *(pa+1);    /* = a[1] */
x = pa[2];      /* = a[2] — subscrito funciona com ponteiro */
```

- `a[i]` é convertido internamente para `*(a+i)`
- `&a[i]` == `a+i`
- Nome de array = ponteiro para a[0], mas **não é variável**: `a = pa` e `a++` são ilegais
- Parâmetro `char s[]` e `char *s` são equivalentes — preferir `char *s` (mais explícito)

```c
/* strlen com ponteiro */
int strlen(char *s)
{
    int n;
    for (n = 0; *s != '\0'; s++)
        n++;
    return n;
}
```

---

## 5.4 Aritmética de ponteiros

Operações válidas:

| Operação | Significado |
|---|---|
| `p + n` | Endereço do n-ésimo objeto após p |
| `p - n` | Endereço do n-ésimo objeto antes de p |
| `p++`, `p--` | Avança/recua um objeto |
| `p - q` | Número de objetos entre p e q (mesmo array) |
| `p < q`, `p == q` | Comparação (mesmo array; `== 0` sempre válido) |

n é **escalado** pelo tamanho do tipo: `int *p; p+1` avança `sizeof(int)` bytes.

```c
/* strlen com subtração de ponteiros */
int strlen(char *s)
{
    char *p = s;
    while (*p != '\0')
        p++;
    return p - s;    /* número de chars */
}
```

**Inválido**: somar dois ponteiros; multiplicar, dividir, deslocar; comparar ponteiros de arrays diferentes.

`NULL` (definido em `<stdio.h>`) representa ponteiro nulo — nunca endereço válido:

```c
char *alloc(int n)
{
    if (espaço_disponível >= n) { ... return ponteiro; }
    else return NULL;   /* sinal de falha */
}
```

---

## 5.5 Ponteiros para char e strings

String literal = array de char com `'\0`', acessado via ponteiro:

```c
char amessage[] = "now is the time";   /* array — conteúdo modificável */
char *pmessage = "now is the time";    /* ponteiro para constante — NÃO modificar conteúdo */
```

`pmessage` pode ser reatribuído; `amessage` não. Tentar modificar string constante via ponteiro é comportamento indefinido.

```c
/* strcpy com ponteiros — forma idiomática */
void strcpy(char *s, char *t)
{
    while (*s++ = *t++)
        ;
}
```

---

## 5.6 Arrays de ponteiros

```c
char *lineptr[MAXLINES];   /* array de ponteiros para char */
```

Cada `lineptr[i]` aponta para uma string (array de char). Útil para ordenar strings sem mover os dados:

```c
/* ordenar trocando ponteiros, não strings */
char *temp = lineptr[i];
lineptr[i] = lineptr[j];
lineptr[j] = temp;
```

`char *argv[]` de `main` é exatamente isso — array de ponteiros para strings dos argumentos.

---

## 5.11 Ponteiros para funções

Função não é variável, mas ponteiro para função pode ser atribuído, passado e retornado:

```c
/* declaração: pfunc aponta para função que recebe dois char* e retorna int */
int (*pfunc)(char *, char *);

pfunc = strcmp;           /* atribui função */
(*pfunc)(s1, s2);         /* chama via ponteiro */
```

Uso: passar estratégia de comparação para algoritmo de ordenação genérico.

```c
void qsort(void *v[], int left, int right,
           int (*comp)(void *, void *))
{
    ...
    if ((*comp)(v[i], v[j]) < 0) ...
}
```

Chamada: `qsort(array, 0, n-1, strcmp)` ou `qsort(array, 0, n-1, numcmp)`.

---

## Ver também
- [[Arrays em C]] — equivalência array/ponteiro; strings
- [[Funções em C]] — call by value; passagem de array
- [[Escopo em C]] — variáveis automáticas; `static`
- [[Argumentos de Linha de Comando em C]] — `argc`, `argv`
- [[Structs em C]] — ponteiros para struct, `->` operator
