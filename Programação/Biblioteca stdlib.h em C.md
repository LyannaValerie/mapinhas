---
title: Biblioteca stdlib.h em C
aliases:
  - stdlib.h
  - atoi C
  - strtol C
  - qsort C
  - bsearch C
  - rand C
  - funções utilitárias C
tags:
  - computação/programação/c
date: 2026-04-21
---

# Biblioteca stdlib.h em C

## Definição

`<stdlib.h>` declara funções de conversão de números, geração de números aleatórios, ordenação, busca, controle de processo e variáveis de ambiente. Alocação dinâmica (`malloc`/`free`) também é aqui — ver [[Alocação Dinâmica de Memória em C]].

Fonte: K&R Appendix B, §B.5.

## Relações (SPO)

| Sujeito | Predicado | Objeto |
|---|---|---|
| `atoi`/`atof`/`atol` | convertem | string → número (sem detecção de erro) |
| `strtod`/`strtol`/`strtoul` | convertem | string → número com ponteiro para sufixo não-convertido |
| `qsort` | ordena | array de qualquer tipo via função de comparação |
| `bsearch` | busca | elemento em array ordenado via função de comparação |
| `rand` | gera | inteiro pseudo-aleatório em `[0, RAND_MAX]` |

## Conversão de strings em números

```c
double atof(const char *s)    /* string → double; equiv: strtod(s, NULL) */
int    atoi(const char *s)    /* string → int;    equiv: (int)strtol(s, NULL, 10) */
long   atol(const char *s)    /* string → long;   equiv: strtol(s, NULL, 10) */
```

> [!warning] `atoi`/`atof` não detectam erro
> Se a string for inválida, o comportamento é indefinido. Prefira `strtol`/`strtod` para código robusto.

```c
double strtod(const char *s, char **endp)
/* converte prefixo de s → double; *endp aponta para sufixo restante */
/* overflow → HUGE_VAL com sinal correto, errno = ERANGE */
/* underflow → 0, errno pode ser ERANGE */

long strtol(const char *s, char **endp, int base)
/* base 2-36; base 0 → detecta 0x (hex), 0 (octal), outro (decimal) */
/* overflow → LONG_MAX ou LONG_MIN, errno = ERANGE */

unsigned long strtoul(const char *s, char **endp, int base)
/* como strtol, mas retorna unsigned long */
```

### Exemplo com detecção de erro

```c
char *end;
long n = strtol("42xyz", &end, 10);
/* n = 42, end aponta para "xyz" */
if (*end != '\0') { /* conversão incompleta */ }
```

## Números aleatórios

```c
int rand(void)             /* retorna inteiro pseudo-aleatório em [0, RAND_MAX] */
void srand(unsigned seed)  /* inicializa semente; srand(1) = estado inicial */
```

```c
srand(time(NULL));                    /* semente baseada em tempo */
int r = rand() % 100;                 /* inteiro em [0, 99] */
double f = (double)rand() / RAND_MAX; /* double em [0.0, 1.0] */
```

> [!info] `RAND_MAX` ≥ 32767 (pode ser muito maior dependendo da implementação)

## Ordenação e busca

```c
void qsort(void *base, size_t n, size_t size,
           int (*cmp)(const void *, const void *))
/* ordena base[0..n-1] em ordem crescente; cmp retorna <0, 0, >0 */

void *bsearch(const void *key, const void *base,
              size_t n, size_t size,
              int (*cmp)(const void *keyval, const void *datum))
/* busca binária em base[0..n-1] ordenado por cmp */
/* retorna ponteiro para elemento encontrado ou NULL */
```

```c
/* exemplo: ordenar array de int */
int cmp_int(const void *a, const void *b) {
    return (*(int*)a - *(int*)b);
}
int arr[] = {3, 1, 4, 1, 5};
qsort(arr, 5, sizeof(int), cmp_int);

int key = 4;
int *found = bsearch(&key, arr, 5, sizeof(int), cmp_int);
```

> [!warning] `cmp_int` com subtração pode ter overflow para inteiros muito grandes/negativos. Use comparação explícita `<`/`>` em código de produção.

## Controle de processo

```c
void abort(void)              /* termina anormalmente (SIGABRT); gera core dump */
void exit(int status)         /* termina normalmente; chama funções atexit, fecha streams */
int  atexit(void (*fcn)(void)) /* registra função a chamar na saída; retorna não-zero se falha */
```

```c
atexit(cleanup);   /* cleanup() chamada ao exit() */
exit(EXIT_SUCCESS);  /* EXIT_SUCCESS=0, EXIT_FAILURE=1 */
```

## Ambiente e sistema

```c
int   system(const char *s)         /* executa comando do shell; NULL → testa se shell existe */
char *getenv(const char *name)      /* retorna valor da variável de ambiente ou NULL */
```

```c
char *path = getenv("PATH");
system("ls -l");   /* executa via sh */
```

## Ver também

- [[Alocação Dinâmica de Memória em C]] — `malloc`, `calloc`, `free`, `realloc`
- [[Biblioteca string.h]] — `strtok`, conversão de chars
- [[Biblioteca math.h]] — funções matemáticas
- [[Biblioteca time.h]] — `time(NULL)` para semente de `rand`
- [[Sinais em C]] — `SIGABRT` disparado por `abort()`
