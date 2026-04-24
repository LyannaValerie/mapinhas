---
title: Descritores de Arquivo UNIX
aliases:
  - file descriptor
  - descritor de arquivo
  - fd UNIX
  - open creat close C
  - read write syscall C
  - lseek C
  - chamadas de sistema I/O C
tags:
  - computação/fundamentos
date: 2026-04-21
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Programação]]

# Descritores de Arquivo UNIX

## Definição

Inteiro não-negativo retornado pelo SO ao abrir arquivo. Identifica o arquivo em todas as operações de I/O subsequentes. No UNIX, todo dispositivo (teclado, tela, disco) é arquivo — interface homogênea única.

## Relações (SPO)
- Descritor `0` → é → `stdin`
- Descritor `1` → é → `stdout`
- Descritor `2` → é → `stderr`
- `open`/`creat` → retornam → descritor ou `-1` em erro
- `close` → libera → descritor para reuso
- `read`/`write` → transferem → bytes entre buffer do processo e arquivo
- `lseek` → reposiciona → cursor do arquivo sem transferir dados

---

## read e write

```c
int n_read    = read (int fd, char *buf, int n);
int n_written = write(int fd, char *buf, int n);
```

- `read` retorna `0` em EOF, `-1` em erro; pode retornar menos que `n` bytes
- `write` retorna bytes escritos; `-1` em erro; se ≠ `n` → erro

Tamanho de `n` típico: `1` (sem buffer) ou bloco físico (`1024`, `4096`) — blocos maiores reduzem syscalls.

```c
#include "syscalls.h"

main()    /* copiar stdin para stdout */
{
    char buf[BUFSIZ];
    int n;
    while ((n = read(0, buf, BUFSIZ)) > 0)
        write(1, buf, n);
    return 0;
}
```

---

## open e creat

```c
#include <fcntl.h>

int open (char *name, int flags, int perms);
int creat(char *name, int perms);
```

Flags (definidas em `<fcntl.h>`):

| Flag | Significado |
|---|---|
| `O_RDONLY` | Somente leitura |
| `O_WRONLY` | Somente escrita |
| `O_RDWR` | Leitura e escrita |

`creat`: cria arquivo novo ou trunca existente para zero. Permissões em octal — ex: `0666` (leitura+escrita para dono, grupo, outros).

---

## close e unlink

```c
int  close (int fd)          /* libera descritor; sem buffer para descarregar */
void unlink(char *name)      /* remove arquivo do sistema de arquivos */
```

`close` ↔ `fclose` (mas sem buffer). `unlink` ↔ `remove` da stdlib.

Limite típico: ~20 arquivos abertos por processo → reutilizar descritores em programas que processam muitos arquivos.

---

## lseek — acesso aleatório

```c
long lseek(int fd, long offset, int origin);
```

| `origin` | Posição base |
|---|---|
| `0` | Início do arquivo |
| `1` | Posição atual |
| `2` | Fim do arquivo |

```c
lseek(fd, 0L, 2);   /* move para o fim — para append */
lseek(fd, 0L, 0);   /* volta ao início — rewind */
```

Retorna nova posição (`long`) ou `-1` em erro.

`fseek(FILE *fp, long offset, int origin)` — versão da stdlib; retorna status não-zero em erro.

---

## getchar de baixo nível

```c
/* versão sem buffer: uma syscall por char */
int getchar(void)
{
    char c;
    return (read(0, &c, 1) == 1) ? (unsigned char) c : EOF;
}

/* versão com buffer: lê BUFSIZ chars por vez */
int getchar(void)
{
    static char buf[BUFSIZ];
    static char *bufp = buf;
    static int n = 0;
    if (n == 0) {
        n = read(0, buf, sizeof buf);
        bufp = buf;
    }
    return (--n >= 0) ? (unsigned char) *bufp++ : EOF;
}
```

`c` deve ser `char` — `read` exige `char *`. Cast para `unsigned char` no retorno evita extensão de sinal negativo.

---

## Relação com FILE *

`FILE *` da stdlib é camada sobre descritores. `FILE` armazena `fd` internamente, adiciona buffer e flags. `fopen` chama `open`/`creat`; `getc` chama `read` via `_fillbuf` quando buffer esgota.

O shell muda os descritores 0/1/2 antes de executar o programa — o programa não sabe se lê de teclado ou de arquivo, de tela ou de pipe.

---

## Ver também
- [[Arquivos em C]] — FILE*, fopen/fclose (camada de alto nível sobre descritores)
- [[Entrada e Saída Padrão em C]] — stdin/stdout/stderr como fds 0/1/2
- [[Alocação Dinâmica de Memória em C]] — sbrk, outra syscall UNIX
