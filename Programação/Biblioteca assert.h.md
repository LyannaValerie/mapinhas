---
title: Biblioteca assert.h
aliases:
  - assert.h
  - assert C
  - NDEBUG
  - static_assert C
  - asserção C
tags:
  - computação/programação/c
date: 2026-04-21
---

# Biblioteca assert.h

## Definição

`<assert.h>` fornece a macro `assert()` para diagnóstico e depuração em tempo de execução. Avalia uma expressão; se falsa, imprime mensagem em `stderr` e chama `abort()`. Desativada em produção via `NDEBUG`.

## Relações (SPO)

| Sujeito | Predicado | Objeto |
|---|---|---|
| `assert(expr)` | avalia | expressão escalar em tempo de execução |
| expressão falsa | dispara | mensagem em `stderr` + `abort()` |
| `NDEBUG` definido | elimina | todo código dentro de `assert()` |
| `static_assert` | verifica | condição em tempo de compilação |
| `abort()` | está em | `[[Biblioteca stdlib.h em C]]` |

## `assert(expressão)`

Única funcionalidade clássica do cabeçalho. Avalia expressão escalar:

- **verdadeira** (≠ 0): sem efeito, execução continua
- **falsa** (= 0): imprime em `stderr` arquivo-fonte, número de linha, nome de função e texto da expressão; chama `abort()`

```c
#include <assert.h>

void calcular_desconto(float preco, float desconto) {
    assert(desconto >= 0.0 && desconto <= preco);
    float preco_final = preco - desconto;
}
```

Falha produz mensagem como:
```
Assertion failed: (desconto >= 0.0 && desconto <= preco), function calcular_desconto, file main.c, line 4.
```

## Desativando com `NDEBUG`

Definir `NDEBUG` antes de `#include <assert.h>` substitui todo `assert(expr)` por `((void)0)` — o código interno não é compilado nem executado.

```c
#define NDEBUG
#include <assert.h>
```

Na prática, via flag de compilador:

```bash
gcc -O3 -DNDEBUG meu_programa.c -o meu_programa
```

`NDEBUG` é uma [[Pré-processador C|macro de pré-processador]] — não afeta `static_assert`.

## Boas Práticas

> [!warning] Nunca coloque efeitos colaterais dentro de `assert`
> Com `NDEBUG` ativo, o código desaparece. Chamadas de função, incrementos e atribuições dentro de `assert` não serão executados em produção.
>
> **Errado:** `assert(processar_dados() == 1);`
>
> **Certo:**
> ```c
> int resultado = processar_dados();
> assert(resultado == 1);
> ```

> [!info] `assert` não é tratamento de erro
> Uso correto: invariantes internas, pré-condições de funções, estados que **nunca deveriam ocorrer** se o código estiver correto.
>
> Uso incorreto: validar entrada do usuário, arquivo não encontrado, falhas de alocação — esses são erros de runtime previsíveis; tratar com `if`/`else` e mensagem adequada.

## `static_assert` (C11)

Verifica condição em **tempo de compilação**. Se falsa, impede a compilação com mensagem de erro. Não é afetada por `NDEBUG`.

```c
static_assert(expressão_constante, "Mensagem de erro");
```

```c
#include <assert.h>

static_assert(sizeof(int) >= 4, "int deve ter pelo menos 4 bytes");
```

- Nome `static_assert` requer `<assert.h>` (C11); o nome nativo é `_Static_assert` (sem o cabeçalho)
- Útil para garantias de portabilidade: tamanho de tipos, alinhamento, valores de constantes

## Ver também

- [[Biblioteca stdlib.h em C]] — `abort()` e `exit()`
- [[Pré-processador C]] — `#define`, `NDEBUG`, compilação condicional
- [[Sinais em C]] — `SIGABRT` disparado por `abort()`
- [[Funções em C]] — pré-condições e contratos de função
