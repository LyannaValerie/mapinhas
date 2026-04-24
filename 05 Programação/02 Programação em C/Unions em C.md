---
title: Unions em C
aliases:
  - union C
  - tipo variante C
  - bit-field C
  - campo de bits C
tags:
  - computação/fundamentos
date: 2026-04-21
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Programação]]

# Unions em C

## Definição

`union` é uma variável que pode conter, em momentos diferentes, objetos de tipos e tamanhos distintos. Todos os membros compartilham o mesmo espaço de memória. Tamanho = tamanho do maior membro.

## Relações (SPO)
- `union` → compartilha → mesmo espaço entre todos os membros
- `union` membro → pode ser → `int`, `float`, `char*` ou qualquer tipo
- Acesso a tipo errado → resulta em → comportamento definido pela implementação
- `union` → útil quando → um campo pode conter apenas um tipo por vez

---

## 6.8 Union

```c
union u_tag {
    int ival;
    float fval;
    char *sval;
} u;
```

- `u` ocupa espaço suficiente para o maior membro (`char *` ou `float`, dependendo da máquina)
- Atribuir e ler o mesmo tipo é correto; ler tipo diferente do armazenado é indefinido
- Programador responsável por rastrear qual tipo está ativo

```c
int utype;   /* rastreia tipo atual */

if (utype == INT)
    printf("%d\n", u.ival);
else if (utype == FLOAT)
    printf("%f\n", u.fval);
else if (utype == STRING)
    printf("%s\n", u.sval);
```

### Union dentro de struct

```c
struct {
    char *name;
    int flags;
    int utype;
    union {
        int ival;
        float fval;
        char *sval;
    } u;
} symtab[NSYM];

symtab[i].u.ival        /* acesso ao membro int */
symtab[i].u.sval[0]     /* primeiro char da string */
```

### Operações e inicialização

- Mesmas operações de struct: copiar, atribuir, `&`, acessar membros
- Inicialização somente com valor do **primeiro** membro:
  ```c
  union u_tag x = { 42 };   /* inicializa ival */
  ```

---

## 6.9 Bit-fields

Campos de bits permitem empacotar vários objetos em uma palavra:

```c
struct {
    unsigned int is_keyword : 1;
    unsigned int is_extern  : 1;
    unsigned int is_static  : 1;
} flags;
```

- Número após `:` = largura em bits
- Usados como inteiros pequenos:

```c
flags.is_extern = flags.is_static = 1;   /* ativa bits */
flags.is_extern = flags.is_static = 0;   /* desativa */
if (flags.is_extern == 0 && flags.is_static == 0) { ... }
```

Alternativa com máscaras e bitwise:
```c
#define KEYWORD  01
#define EXTERNAL 02
#define STATIC   04

flags |= EXTERNAL | STATIC;         /* ativa */
flags &= ~(EXTERNAL | STATIC);      /* desativa */
if ((flags & (EXTERNAL | STATIC)) == 0) { ... }
```

> [!warning] Portabilidade de bit-fields
> - Ordem de atribuição (left-to-right vs right-to-left) varia por máquina
> - Campos não têm endereço — `&` inaplicável
> - Campos não são arrays
> - Declarar `signed` ou `unsigned` explicitamente para portabilidade
> - Não usar bit-fields para formatos externos de dados (ex: protocolos de rede) — não portável

---

## Ver também
- [[Structs em C]] — sintaxe idêntica; operações análogas
- [[Tipos de Dados C]] — operadores bitwise
- [[Operadores C]] — `|`, `&`, `~`, `<<`, `>>`
