---
title: Biblioteca time.h
aliases:
  - time.h
  - time_t C
  - struct tm C
  - strftime C
  - funções de tempo C
tags:
  - computação/programação/c
date: 2026-04-21
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Programação]]

# Biblioteca time.h

## Definição

`<time.h>` declara tipos e funções para manipulação de datas e horas. Tipos principais: `clock_t` (tempo de CPU), `time_t` (tempo calendário), `struct tm` (tempo decomposto).

Fonte: K&R Appendix B, §B.10.

## Relações (SPO)

| Sujeito | Predicado | Objeto |
|---|---|---|
| `time_t` | representa | tempo calendário (normalmente segundos desde epoch) |
| `clock_t` | representa | tempo de processador consumido |
| `struct tm` | decompõe | `time_t` em campos legíveis (ano, mês, dia...) |
| `CLOCKS_PER_SEC` | define | unidades de `clock_t` por segundo |

## Estrutura `struct tm`

```c
struct tm {
    int tm_sec;    /* segundos desde o minuto (0-61; até 2 leap seconds) */
    int tm_min;    /* minutos desde a hora (0-59) */
    int tm_hour;   /* horas desde meia-noite (0-23) */
    int tm_mday;   /* dia do mês (1-31) */
    int tm_mon;    /* meses desde janeiro (0-11) */
    int tm_year;   /* anos desde 1900 */
    int tm_wday;   /* dias desde domingo (0-6) */
    int tm_yday;   /* dias desde 1º de janeiro (0-365) */
    int tm_isdst;  /* flag de horário de verão (>0 ativo, 0 inativo, <0 desconhecido) */
};
```

## Funções

```c
clock_t clock(void)
/* tempo de CPU usado pelo programa; divida por CLOCKS_PER_SEC para segundos */
/* retorna -1 se não disponível */

time_t time(time_t *tp)
/* tempo calendário atual; se tp != NULL, também armazena em *tp */

double difftime(time_t t2, time_t t1)
/* retorna t2 - t1 em segundos */

time_t mktime(struct tm *tp)
/* converte tempo local decomposto (struct tm) → time_t */
/* normaliza campos fora do intervalo; retorna -1 se falhar */
```

## Conversão `time_t` ↔ string

```c
char *asctime(const struct tm *tp)
/* "Fri Apr 21 10:30:00 2026\n" — formato fixo */

char *ctime(const time_t *tp)
/* equiv: asctime(localtime(tp)) */

struct tm *gmtime(const time_t *tp)    /* converte → UTC (struct tm estático) */
struct tm *localtime(const time_t *tp) /* converte → horário local (struct tm estático) */
```

> [!warning] Buffers estáticos
> `asctime`, `ctime`, `gmtime`, `localtime` retornam ponteiros para buffers estáticos internos. Chamadas sucessivas sobrescrevem. Copie se precisar preservar.

## Formatação customizada: `strftime`

```c
size_t strftime(char *s, size_t smax, const char *fmt, const struct tm *tp)
/* grava string formatada em s (até smax-1 chars); retorna nº de chars gravados */
```

| Especificador | Significado |
|---|---|
| `%Y` | ano com século (ex: 2026) |
| `%y` | ano sem século (00-99) |
| `%m` | mês (01-12) |
| `%d` | dia do mês (01-31) |
| `%H` | hora (00-23) |
| `%M` | minuto (00-59) |
| `%S` | segundo (00-61) |
| `%A` / `%a` | nome do dia (completo / abreviado) |
| `%B` / `%b` | nome do mês (completo / abreviado) |
| `%c` | data e hora locais |
| `%x` | data local |
| `%X` | hora local |
| `%j` | dia do ano (001-366) |
| `%Z` | nome do fuso horário |
| `%%` | `%` literal |

## Exemplo completo

```c
#include <time.h>
#include <stdio.h>

time_t now = time(NULL);
struct tm *lt = localtime(&now);

char buf[64];
strftime(buf, sizeof(buf), "%Y-%m-%d %H:%M:%S", lt);
printf("Agora: %s\n", buf);

/* medir tempo de CPU */
clock_t start = clock();
/* ... trabalho ... */
double elapsed = (double)(clock() - start) / CLOCKS_PER_SEC;
printf("CPU: %.3f s\n", elapsed);
```

## Ver também

- [[Biblioteca stdlib.h em C]] — `srand(time(NULL))` usa `time()`
- [[Biblioteca string.h]] — `strftime` grava em buffer de char
- [[printf em C]] — especificadores de formato para saída formatada
