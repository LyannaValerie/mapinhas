---
title: printf em C
aliases:
  - printf
  - fprintf C
  - sprintf C
  - format string C
  - conversão de saída C
tags:
  - computação/fundamentos
date: 2026-04-21
---

# printf em C

## Definição

Converte valores internos para texto formatado e escreve em `stdout`. Controlado por string de formato com texto literal e especificações de conversão (`%...`). Retorna número de caracteres escritos.

## Relações (SPO)
- `sprintf` → armazena → saída em string (não em `stdout`)
- `fprintf` → escreve → para `FILE *` especificado
- Especificação `%` → controla → conversão do próximo argumento
- `*` como largura/precisão → lê → valor do próximo argumento `int`

---

## Assinaturas

```c
int printf(char *format, arg1, arg2, ...);
int sprintf(char *string, char *format, arg1, arg2, ...);
int fprintf(FILE *fp, char *format, ...);
```

`sprintf` coloca resultado em `string` — que deve ser grande o suficiente para receber a saída.

---

## Estrutura da especificação de conversão

```
% [flags] [largura] [.precisão] [modificador] caractere
```

| Campo | Descrição |
|---|---|
| `-` (flag) | Alinhamento à esquerda |
| número | Largura mínima do campo |
| `.número` | Precisão: chars máx de string / casas decimais / dígitos mínimos de inteiro |
| `h` | Argumento é `short` |
| `l` | Argumento é `long` |

---

## Tabela de caracteres de conversão

| Char | Tipo; Saída |
|---|---|
| `d`, `i` | `int`; decimal |
| `o` | `int`; octal sem prefixo `0` |
| `x`, `X` | `int`; hexadecimal sem `0x`/`0X` |
| `u` | `int`; decimal sem sinal |
| `c` | `int`; caractere único |
| `s` | `char *`; string até `'\0'` ou precisão |
| `f` | `double`; `[-]m.dddddd` (padrão 6 decimais) |
| `e`, `E` | `double`; notação científica |
| `g`, `G` | `double`; `%e` se exp < −4 ou ≥ precisão; senão `%f`; sem zeros à direita |
| `p` | `void *`; ponteiro (representação dependente de implementação) |
| `%` | (nenhum arg); imprime `%` literal |

---

## Exemplos de largura e precisão com `"hello, world"` (12 chars)

| Formato | Saída |
|---|---|
| `%s` | `hello, world` |
| `%10s` | `hello, world` |
| `%.10s` | `hello, wor` |
| `%-10s` | `hello, world` |
| `%15.10s` | `     hello, wor` |
| `%-15.10s` | `hello, wor     ` |

---

## Largura/precisão dinâmica com `*`

```c
printf("%.*s", max, s);   /* imprime no máximo max chars de s */
```

O valor de `*` é lido do próximo argumento `int`.

---

## Segurança

```c
printf(s);          /* PERIGOSO se s contém % */
printf("%s", s);    /* SEGURO */
```

> [!danger] Format string vulnerability
> `printf(s)` com `s` controlado externamente e contendo `%` interpreta bytes arbitrários como conversões — comportamento indefinido, risco de segurança.

---

## Ver também
- [[Entrada e Saída Padrão em C]] — stdin/stdout, putchar
- [[scanf em C]] — entrada formatada, especificadores simétricos
- [[Arquivos em C]] — fprintf para FILE*
- [[Listas de Argumentos Variáveis em C]] — como printf processa `...`
- [[Tipos de Dados C]] — tipos dos argumentos e conversões
