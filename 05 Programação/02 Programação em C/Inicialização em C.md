---
title: Inicialização em C
aliases:
  - inicialização C
  - inicialização de array C
  - inicialização de variável C
tags:
  - computação/fundamentos
date: 2026-04-21
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Programação]]

# Inicialização em C

## Definição

Regras que determinam o valor inicial de uma variável dependendo de sua classe de armazenamento. C trata cada classe diferentemente — externa/estática sempre zero; automática/register indefinida.

## Relações (SPO)
- Variável externa/estática → inicializada a → zero (por padrão)
- Variável automática/register → contém → valor indefinido (lixo)
- Inicializador de externo/estático → deve ser → expressão constante
- Inicializador de automático → pode ser → qualquer expressão (inclusive chamada de função)

---

## Regra por classe de armazenamento

| Classe | Padrão sem inicializador | Inicializador permitido |
|---|---|---|
| Externa | Zero | Expressão constante |
| `static` | Zero | Expressão constante |
| Automática | **Indefinido (lixo)** | Qualquer expressão |
| `register` | **Indefinido (lixo)** | Qualquer expressão |

---

## Escalares

```c
int x = 1;
char squota = '\'';
long day = 1000L * 60L * 60L * 24L;   /* ms/day — constante válida */
```

Para automáticos, o inicializador pode ser qualquer expressão:

```c
int binsearch(int x, int v[], int n)
{
    int low = 0;
    int high = n - 1;   /* usa parâmetro — não é constante */
    int mid;
    ...
}
```

Equivalente a atribuição explícita — questão de estilo.

---

## Arrays

Lista de inicializadores entre `{ }`, separados por vírgula:

```c
int days[] = { 31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31 };
```

- Tamanho omitido → compilador conta os inicializadores (12 aqui)
- Menos inicializadores que o tamanho → restante zerado (externo/estático/automático)
- Mais inicializadores que o tamanho → erro

```c
int a[5] = { 1, 2 };   /* a[2], a[3], a[4] == 0 */
```

> [!warning]
> Não há como especificar repetição de inicializador nem inicializar elemento do meio sem fornecer todos os anteriores.

---

## Arrays de char (strings)

String literal é atalho para lista de chars com `'\0'` no final:

```c
char pattern[] = "ould";
/* equivalente a: */
char pattern[] = { 'o', 'u', 'l', 'd', '\0' };
/* tamanho = 5 */
```

---

## Ver também
- [[Escopo em C]] — classes de armazenamento, externas, automáticas
- [[Variáveis Estáticas em C]] — inicialização de `static`
- [[Arrays em C]] — arrays e strings
- [[Structs em C]] — inicialização de structs
