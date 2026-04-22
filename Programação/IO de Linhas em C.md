---
title: IO de Linhas em C
aliases:
  - fgets C
  - fputs C
  - gets puts C
  - leitura de linha C
  - getline C
tags:
  - computação/fundamentos
date: 2026-04-21
---

# I/O de Linhas em C

## Definição

Funções para leitura e escrita linha a linha. `fgets`/`fputs` operam sobre `FILE *` arbitrário. `gets`/`puts` fixados em `stdin`/`stdout`. Preferir sempre `fgets` — impõe limite de tamanho.

## Relações (SPO)
- `fgets` → inclui → `'\n'` na string lida
- `fgets` → termina → string com `'\0'`
- `gets` → remove → `'\n'` da string (comportamento diferente de `fgets`)
- `puts` → adiciona → `'\n'` na saída (comportamento diferente de `fputs`)
- Ambas → retornam → `NULL` em EOF ou erro

---

## Assinaturas

```c
char *fgets(char *line, int maxline, FILE *fp)
int   fputs(char *line, FILE *fp)
```

`fgets`: lê até `maxline-1` chars (incluindo `'\n'`), termina com `'\0'`. Retorna `line` ou `NULL`.

`fputs`: escreve string sem adicionar `'\n'`. Retorna `EOF` em erro, valor não-negativo em sucesso.

> [!note] Valores de retorno inconsistentes
> `fgets` retorna ponteiro; `fputs` retorna int. O padrão especifica assim sem razão óbvia.

---

## Implementação (da biblioteca padrão)

```c
/* fgets */
char *fgets(char *s, int n, FILE *iop)
{
    register int c;
    register char *cs = s;
    while (--n > 0 && (c = getc(iop)) != EOF)
        if ((*cs++ = c) == '\n')
            break;
    *cs = '\0';
    return (c == EOF && cs == s) ? NULL : s;
}

/* fputs */
int fputs(char *s, FILE *iop)
{
    int c;
    while (c = *s++)
        putc(c, iop);
    return ferror(iop) ? EOF : 0;
}
```

---

## getline construído sobre fgets

```c
int getline(char *line, int max)
{
    if (fgets(line, max, stdin) == NULL)
        return 0;
    else
        return strlen(line);
}
```

`getline` retorna comprimento — valor `0` sinaliza EOF diretamente. `fgets` retorna `NULL` — requer verificação de ponteiro.

---

## gets e puts (stdin/stdout)

```c
char *gets(char *s)    /* lê linha de stdin; remove '\n'; sem limite */
int   puts(char *s)    /* escreve string + '\n' em stdout */
```

> [!danger] gets é inseguro — não usar
> Sem limite de tamanho de buffer. Buffer overflow clássico. Removida do padrão C11. Substituir por `fgets(s, tamanho, stdin)`.

---

## Ver também
- [[Arquivos em C]] — FILE*, getc/putc; fgets usa getc internamente
- [[Arrays em C]] — buffer de char, terminador `'\0'`
- [[Entrada e Saída Padrão em C]] — stdin/stdout
