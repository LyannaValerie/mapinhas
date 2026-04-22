---
title: Arquivos em C
aliases:
  - FILE C
  - fopen C
  - fclose C
  - arquivo em C
  - ponteiro de arquivo C
  - getc putc C
  - fscanf fprintf C
  - feof ferror C
tags:
  - computação/fundamentos
date: 2026-04-21
---

# Arquivos em C

## Definição

`FILE` é uma estrutura (definida em `<stdio.h>`) que representa um stream de arquivo: buffer, posição, descritor, flags de estado. Operada sempre via `FILE *`. `fopen` abre e retorna o ponteiro; `NULL` em erro.

## Relações (SPO)
- `fopen` → retorna → `FILE *` ou `NULL`
- `fclose` → libera → `FILE *` e descarrega buffer de saída
- `stdin`, `stdout`, `stderr` → são → `FILE *` constantes abertas automaticamente pelo SO
- `getc`/`putc` → leem/escrevem → um char de/para `FILE *`
- `ferror` → retorna → não-zero se ocorreu erro no stream
- `feof` → retorna → não-zero se fim de arquivo atingido

---

## fopen e modos

```c
FILE *fopen(char *name, char *mode);

fp = fopen(name, mode);
```

| Modo | Comportamento |
|---|---|
| `"r"` | Leitura; erro se não existir |
| `"w"` | Escrita; descarta conteúdo anterior ou cria |
| `"a"` | Append; preserva conteúdo; cria se não existir |
| sufixo `"b"` | Modo binário (sistemas que distinguem texto/binário) |

Se erro → retorna `NULL`. Tentar ler arquivo inexistente, sem permissão, etc. → `NULL`.

---

## getc e putc

```c
int getc(FILE *fp)         /* retorna próximo char ou EOF */
int putc(int c, FILE *fp)  /* escreve c; retorna c ou EOF em erro */
```

Podem ser macros — sem overhead de chamada. Equivalentes a `getchar`/`putchar` com `FILE *`:

```c
#define getchar()    getc(stdin)
#define putchar(c)   putc((c), stdout)
```

---

## fscanf e fprintf

```c
int fscanf(FILE *fp, char *format, ...)
int fprintf(FILE *fp, char *format, ...)
```

Idênticos a `scanf`/`printf`; primeiro argumento é o `FILE *`.

---

## fclose

```c
int fclose(FILE *fp)
```

- Desfaz associação `FILE *` ↔ arquivo
- Libera o ponteiro para reuso
- **Descarrega buffer de saída** — crítico para garantir que dados buffered sejam gravados
- Chamado automaticamente em terminação normal do programa

Limite típico: ~20 arquivos abertos simultaneamente → liberar quando não precisar mais.

---

## Tratamento de erros: stderr e exit

`stderr` → aparece na tela mesmo com `stdout` redirecionado.

```c
/* cat: versão com tratamento de erro correto */
main(int argc, char *argv[])
{
    FILE *fp;
    char *prog = argv[0];

    while (--argc > 0)
        if ((fp = fopen(*++argv, "r")) == NULL) {
            fprintf(stderr, "%s: can't open %s\n", prog, *argv);
            exit(1);
        } else {
            filecopy(fp, stdout);
            fclose(fp);
        }
    if (ferror(stdout)) {
        fprintf(stderr, "%s: error writing stdout\n", prog);
        exit(2);
    }
    exit(0);
}
```

Convenção: `exit(0)` = sucesso, não-zero = erro. `exit` ≡ `return` em `main`, mas pode ser chamada de qualquer função.

---

## ferror e feof

```c
int ferror(FILE *fp)   /* não-zero se ocorreu erro */
int feof(FILE *fp)     /* não-zero se EOF atingido */
```

Erros de saída são raros mas ocorrem (ex: disco cheio) — programa sério deve verificar `ferror(stdout)`.

---

## stdin, stdout, stderr como FILE *

São objetos `FILE *` — **constantes**, não variáveis. Não podem ser reatribuídos diretamente. Podem ser redirecionados via `freopen`.

---

## Estrutura interna (implementação — Ch8)

```c
typedef struct _iobuf {
    int   cnt;    /* chars restantes no buffer */
    char *ptr;    /* próxima posição no buffer */
    char *base;   /* início do buffer */
    int   flag;   /* modo: _READ, _WRITE, _UNBUF, _EOF, _ERR */
    int   fd;     /* descritor de arquivo UNIX subjacente */
} FILE;
```

`getc` como macro: decrementa `cnt`, avança `ptr`, retorna char. Chama `_fillbuf` somente quando buffer esgota — minimiza syscalls.

```c
#define getc(p) (--(p)->cnt >= 0 \
    ? (unsigned char) *(p)->ptr++ : _fillbuf(p))
```

`stderr` inicializado como não-bufferizado (`_UNBUF`) — mensagens de erro aparecem imediatamente.

---

## Ver também
- [[Entrada e Saída Padrão em C]] — stdin/stdout, getchar/putchar
- [[printf em C]] — fprintf
- [[scanf em C]] — fscanf
- [[IO de Linhas em C]] — fgets, fputs
- [[Descritores de Arquivo UNIX]] — fd subjacente, implementação de baixo nível
