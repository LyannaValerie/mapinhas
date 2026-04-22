---
title: scanf em C
aliases:
  - scanf
  - sscanf C
  - fscanf C
  - entrada formatada C
  - conversão de entrada C
tags:
  - computação/fundamentos
date: 2026-04-21
---

# scanf em C

## Definição

Análogo de `printf` para entrada. Lê de `stdin`, interpreta conforme string de formato, armazena via ponteiros. Retorna número de itens convertidos com sucesso, ou `EOF` em fim de arquivo.

## Relações (SPO)
- `scanf` → lê de → `stdin`
- `sscanf` → lê de → string em memória
- `fscanf` → lê de → `FILE *` especificado
- Todos os argumentos → devem ser → ponteiros
- Retorno `0` → indica → falha de conversão (≠ `EOF`, que indica fim de arquivo)

---

## Assinaturas

```c
int scanf(char *format, ...);
int sscanf(char *string, char *format, arg1, arg2, ...);
int fscanf(FILE *fp, char *format, ...);
```

---

## String de formato

Pode conter:
- Espaços/tabs — **não** ignorados durante a leitura
- Caracteres ordinários (não `%`) — devem casar com próximo não-espaço da entrada
- Especificações de conversão: `%[*][largura][h|l|L]caractere`
  - `*` → suprime atribuição (campo lido mas descartado)
  - `largura` → máximo de chars a consumir
  - `h/l` → tamanho do alvo (`short *` ou `long *`)

---

## Tabela de caracteres de conversão

| Char | Entrada; Tipo do argumento |
|---|---|
| `d` | inteiro decimal; `int *` |
| `i` | inteiro decimal/octal/hex; `int *` |
| `o` | octal (com ou sem `0`); `int *` |
| `u` | decimal sem sinal; `unsigned int *` |
| `x` | hexadecimal (com ou sem `0x`); `int *` |
| `c` | caracteres; `char *` (não pula espaço — usar `%1s` para isso) |
| `s` | string sem aspas; `char *` (adiciona `'\0'` automaticamente) |
| `e`,`f`,`g` | float com sinal/ponto/expoente opcionais; `float *` |
| `%` | `%` literal; sem atribuição |

`d`, `i`, `o`, `u`, `x` aceitam prefixo `h` (`short *`) ou `l` (`long *`).

---

## Exemplos

```c
/* calculadora rudimentar */
double sum = 0, v;
while (scanf("%lf", &v) == 1)
    printf("\t%.2f\n", sum += v);
```

```c
/* "25 Dec 1988" */
int day, year;
char monthname[20];
scanf("%d %s %d", &day, monthname, &year);
/* array não precisa de & */
```

```c
/* "mm/dd/yy" */
int day, month, year;
scanf("%d/%d/%d", &month, &day, &year);
```

---

## sscanf para parsing flexível

```c
while (getline(line, sizeof(line)) > 0) {
    if (sscanf(line, "%d %s %d", &day, monthname, &year) == 3)
        printf("valid: %s\n", line);
    else if (sscanf(line, "%d/%d/%d", &month, &day, &year) == 3)
        printf("valid: %s\n", line);
    else
        printf("invalid: %s\n", line);
}
```

Padrão: ler linha inteira, tentar múltiplos formatos — mais robusto que `scanf` direto.

---

## Erro mais comum

```c
scanf("%d", n);    /* ERRADO — n não é ponteiro */
scanf("%d", &n);   /* CORRETO */
```

> [!danger] Argumento sem `&`
> Passa valor onde se espera ponteiro. Não detectado em compilação. Comportamento indefinido — corrupção de memória.

---

## Interação com outras funções de entrada

`scanf` pode ser intercalado com `getchar`/`fgets`. A próxima chamada começa no primeiro caractere não consumido por `scanf`.

---

## Ver também
- [[printf em C]] — análogo para saída, mesma filosofia de conversão
- [[Entrada e Saída Padrão em C]] — getchar, stdin
- [[Arquivos em C]] — fscanf/fprintf
- [[Ponteiros em C]] — por que todos os argumentos são ponteiros
