---
title: Qualificadores de Tipo em C
aliases:
  - const C
  - volatile C
  - type qualifiers C
tags:
  - computação/programação/c
date: 2026-04-21
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Programação]]

# Qualificadores de Tipo em C

## Definição

Qualificadores modificam propriedades de um objeto sem alterar seu tipo ou intervalo de valores. Definidos em A.4.4 do manual de referência ANSI C.

## Relações (SPO)

| Sujeito | Predicado | Objeto |
|---|---|---|
| `const` | declara objeto como | imutável após inicialização |
| `volatile` | sinaliza ao compilador | não otimizar acesso ao objeto |
| qualificador | não afeta | intervalo de valores ou aritmética |
| qualificador | combina com | qualquer tipo de objeto |

## `const`

Declara que o valor do objeto não será alterado após inicialização.

```c
const int MAX = 100;     /* não pode ser modificado */
const char *p;           /* ponteiro para char constante */
char * const p;          /* ponteiro constante para char mutável */
const char * const p;    /* ponteiro constante para char constante */
```

> [!warning] `const` ≠ imutável em runtime
> O compilador pode não reclamar se um `const` for modificado via cast para ponteiro não-const. Comportamento é indefinido.

Uso principal: parâmetros de função que não devem ser modificados.

```c
int strlen(const char *s);   /* s não será alterado dentro da função */
```

## `volatile`

Sinaliza que o objeto pode ser modificado por forças externas ao compilador (hardware, outra thread, handler de sinal). Impede otimizações de cache em registrador.

```c
volatile int status;          /* lido do hardware — não cachear */
volatile sig_atomic_t flag;   /* modificado por handler de sinal */
```

> [!info] Quando usar `volatile`
> - Registradores mapeados em memória
> - Variáveis compartilhadas com handlers de sinal
> - Variáveis compartilhadas entre threads (sem garantia de ordering — use também primitivas de sincronização)

## Combinação com ponteiros

```c
const volatile int *p;   /* ponteiro para objeto const e volatile */
```

## Ver também

- [[Tipos de Dados C]] — tipos básicos e derivados
- [[Lvalue e Objeto em C]] — o que é um objeto em C
- [[Sinais em C]] — uso de `volatile sig_atomic_t`
- [[Variáveis Estáticas em C]] — qualificação de variáveis estáticas
