---
title: Listas de Argumentos Variáveis em C
aliases:
  - stdarg C
  - va_list C
  - va_start
  - va_arg
  - va_end
  - variadic function C
  - função variádica C
  - minprintf
tags:
  - computação/fundamentos
date: 2026-04-21
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Programação]]

# Listas de Argumentos Variáveis em C

## Definição

Mecanismo ANSI C para funções com número e tipos de argumentos variáveis. Declarados com `...` no protótipo. Processados via macros de `<stdarg.h>`. Implementação varia por máquina; interface é uniforme.

## Relações (SPO)
- `...` → declara → lista variável (obrigatoriamente no fim do protótipo)
- `va_list` → é → tipo de variável que percorre os argumentos variáveis
- `va_start(ap, último_fixo)` → inicializa → `ap` para o primeiro argumento variável
- `va_arg(ap, tipo)` → retorna → próximo argumento e avança `ap`
- `va_end(ap)` → finaliza → uso da lista (obrigatório antes de `return`)

---

## Macros de `<stdarg.h>`

```c
#include <stdarg.h>

void f(char *fmt, ...)
{
    va_list ap;
    va_start(ap, fmt);      /* último parâmetro nomeado: fmt */

    int ival    = va_arg(ap, int);
    double dval = va_arg(ap, double);
    char *sval  = va_arg(ap, char *);

    va_end(ap);             /* obrigatório antes de retornar */
}
```

> [!warning] Mínimo de um argumento nomeado
> `va_start` usa o último parâmetro nomeado para localizar o início da lista variável. Protótipo com apenas `...` e nenhum parâmetro fixo é inválido.

> [!warning] `va_arg` não detecta fim
> O chamador deve codificar o número/tipo dos argumentos na string de formato ou via argumento extra. Ler além do último argumento é comportamento indefinido.

---

## Implementação de `minprintf`

```c
#include <stdarg.h>

/* minprintf: printf mínimo com lista variável de argumentos */
void minprintf(char *fmt, ...)
{
    va_list ap;
    char *p, *sval;
    int ival;
    double dval;

    va_start(ap, fmt);
    for (p = fmt; *p; p++) {
        if (*p != '%') {
            putchar(*p);
            continue;
        }
        switch (*++p) {
        case 'd':
            ival = va_arg(ap, int);
            printf("%d", ival);
            break;
        case 'f':
            dval = va_arg(ap, double);
            printf("%f", dval);
            break;
        case 's':
            for (sval = va_arg(ap, char *); *sval; sval++)
                putchar(*sval);
            break;
        default:
            putchar(*p);
            break;
        }
    }
    va_end(ap);
}
```

`minprintf` delega as conversões reais ao `printf` — demonstra o padrão sem reimplementar toda a maquinaria de formatação.

---

## vprintf, vfprintf, vsprintf

Variantes da família `printf` que aceitam `va_list` diretamente (em vez de `...`):

```c
#include <stdarg.h>

void error(char *fmt, ...)
{
    va_list args;
    va_start(args, fmt);
    fprintf(stderr, "error: ");
    vfprintf(stderr, fmt, args);   /* passa va_list para fprintf */
    fprintf(stderr, "\n");
    va_end(args);
    exit(1);
}
```

Útil para escrever wrappers sobre funções `printf`-like.

---

## Ver também
- [[printf em C]] — usa internamente esse mecanismo; vprintf/vfprintf
- [[Funções em C]] — protótipos, passagem de argumentos
- [[Pré-processador C]] — macros em geral
