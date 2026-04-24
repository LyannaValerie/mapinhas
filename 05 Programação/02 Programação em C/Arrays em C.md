---
title: Arrays em C
aliases:
  - array C
  - vetor C
  - array de caracteres
  - string C
  - char array
tags:
  - computação/fundamentos
date: 2026-04-21
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Programação]]

# Arrays em C

## Definição

Array é uma sequência contígua de elementos do mesmo tipo na [[Memória]]. Em C, arrays são indexados por inteiro, com subscrito começando em **0**.

## Relações (SPO)
- Array em C → é → sequência contígua de objetos do mesmo tipo
- Array C → passado para função como → ponteiro para o primeiro elemento
- String C → é → array de `char` terminado por `'\0'`
- Subscrito de array → pode ser → qualquer expressão inteira

---

## Declaração e inicialização

```c
int ndigit[10];              /* array de 10 ints; índices 0..9 */

/* inicializar explicitamente */
for (i = 0; i < 10; ++i)
    ndigit[i] = 0;
```

> [!warning] Sem inicialização automática
> Arrays automáticos (locais) **não** são inicializados. Acesso antes de atribuição retorna lixo.

---

## Acesso por subscrito

Subscrito pode ser qualquer expressão inteira:

```c
ndigit[c - '0']    /* c é um char de dígito; c-'0' dá valor 0..9 */
```

Isso funciona porque `'0'`, `'1'`, …, `'9'` têm valores [[ASCII]] consecutivos — propriedade garantida por todos os conjuntos de caracteres.

**Exemplo clássico — contar dígitos, espaços, outros:**

```c
int ndigit[10], nwhite = 0, nother = 0;

while ((c = getchar()) != EOF)
    if (c >= '0' && c <= '9')
        ++ndigit[c - '0'];
    else if (c == ' ' || c == '\n' || c == '\t')
        ++nwhite;
    else
        ++nother;
```

---

## Arrays de caracteres (strings C)

O tipo mais comum de array em C. Uma string é um array de `char` terminado pelo caractere nulo `'\0'` (valor 0).

```c
#define MAXLINE 1000
char line[MAXLINE];     /* array para armazenar uma linha */
```

### Terminador nulo

```c
s[i] = '\0';    /* marca o fim da string */
```

Constantes de string no código-fonte já incluem o terminador automaticamente:

```
"hello\n"  →  armazenado como: h e l l o \n \0
```

Portanto, o armazenamento físico exige **um byte a mais** que os caracteres visíveis.

> [!tip] `strlen` exclui o `'\0'`
> `strlen(s)` retorna o número de caracteres **sem** contar o terminador. Declarada em `<string.h>`.

### `'x'` vs `"x"`

| Notação | Tipo | Descrição |
|---|---|---|
| `'x'` | `int` | Valor inteiro do caractere no conjunto de caracteres |
| `"x"` | `char[]` | Array de dois chars: `'x'` e `'\0'` |

---

## Passagem de array para função

Quando o nome de um array é usado como argumento de função, é passado o **endereço do primeiro elemento** — não uma cópia.

```c
int getline(char s[], int lim);   /* s recebe endereço do array */
void copy(char to[], char from[]);
```

A função pode acessar e modificar qualquer elemento do array original via subscrito.

> [!note] Tamanho do array
> O tamanho do array não precisa ser declarado no parâmetro (`char s[]`), pois a função recebe apenas o ponteiro. O limite (`lim`) deve ser passado como argumento separado.

---

## Exemplo completo — linha mais longa

```c
/* getline: lê linha em s, retorna comprimento */
int getline(char s[], int lim)
{
    int c, i;
    for (i = 0; i < lim-1 && (c = getchar()) != EOF && c != '\n'; ++i)
        s[i] = c;
    if (c == '\n') {
        s[i] = c;
        ++i;
    }
    s[i] = '\0';
    return i;
}

/* copy: copia 'from' em 'to'; assume to tem espaço suficiente */
void copy(char to[], char from[])
{
    int i = 0;
    while ((to[i] = from[i]) != '\0')
        ++i;
}
```

---

## Equivalência array/ponteiro (Ch5)

Array e ponteiro são intercambiáveis na maioria dos contextos:

```c
int a[10];
int *pa = a;    /* pa aponta para a[0]; equivale a &a[0] */

x = *pa;        /* = a[0] */
x = *(pa+1);    /* = a[1] */
x = pa[2];      /* = a[2] — subscrito funciona com ponteiro */
```

- `a[i]` é convertido pelo compilador para `*(a+i)`
- `&a[i]` == `a+i`
- Nome do array é **constante**: `a = pa` e `a++` são ilegais
- Parâmetro `char s[]` e `char *s` são equivalentes — preferir `char *s`

```c
/* strlen com aritmética de ponteiros */
int strlen(char *s)
{
    char *p = s;
    while (*p != '\0')
        p++;
    return p - s;    /* número de chars */
}
```

> [!warning] Array não é variável
> O nome do array não pode ser reatribuído. Ponteiro pode.

---

## Ver também
- [[Tipos de Dados C]] — tipos primitivos; `char` como inteiro pequeno; [[ASCII]]
- [[Ponteiros em C]] — aritmética de ponteiros; equivalência; `char *` strings
- [[Funções em C]] — passagem por valor vs. passagem de array por referência
- [[Escopo em C]] — arrays automáticos vs. externos
- [[Linguagem C]] — contexto geral
