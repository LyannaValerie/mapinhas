---
title: Funções em C
aliases:
  - função C
  - protótipo de função
  - call by value C
  - passagem por valor
  - declaração de função C
tags:
  - computação/fundamentos
date: 2026-04-21
---

# Funções em C

## Definição

Função encapsula uma computação com nome, parâmetros e valor de retorno. Equivalente a subrotina (Fortran), procedimento (Pascal). Permite ignorar *como* algo é feito — apenas *o quê* importa.

## Relações (SPO)
- Função C → recebe argumentos por → valor (cópia)
- Array → passado como → ponteiro (sem cópia dos elementos)
- Protótipo de função → permite → verificação de tipos em tempo de compilação
- `main` → é → função como qualquer outra; retorna status ao SO

---

## Estrutura de uma função

```c
tipo-retorno nome-função(declarações de parâmetros)
{
    declarações
    instruções
}
```

**Exemplo:**

```c
/* power: eleva base à potência n; n >= 0 */
int power(int base, int n)
{
    int p;
    for (p = 1; n > 0; --n)
        p = p * base;
    return p;
}
```

- Parâmetros são **locais** à função — invisíveis fora dela
- `return expressão;` devolve o valor ao chamador
- `return;` sem expressão: retorna controle sem valor útil
- Função que "cai no fim" (`}`) retorna valor indefinido para funções não-`void`

---

## Protótipo de função (ANSI C)

Declaração antes do uso que informa ao compilador os tipos dos argumentos e do retorno:

```c
int power(int m, int n);    /* protótipo */

main()
{
    printf("%d\n", power(2, 5));   /* compilador verifica tipos aqui */
}

int power(int base, int n)         /* definição */
{ ... }
```

> [!warning] Protótipo ≠ definição
> - **Protótipo**: anuncia assinatura; permite verificação de tipos
> - **Definição**: contém o corpo com o código
> Protótipo e definição devem concordar em tipos — discrepância é erro.

Nomes dos parâmetros são opcionais no protótipo:

```c
int power(int, int);    /* válido — nomes omitidos */
```

### Antes do ANSI C (estilo K&R original)

```c
/* estilo antigo — sem tipos nos parâmetros */
power(base, n)
int base, n;
{ ... }
```

O compilador antigo não verificava tipos de argumentos. ANSI C eliminou isso.

---

## Passagem por valor (*call by value*)

Em C, **todos os argumentos são passados por valor** — a função recebe uma cópia, não o original.

```c
int power(int base, int n)
{
    int p;
    for (p = 1; n > 0; --n)   /* n decrementado localmente */
        p = p * base;
    return p;
    /* n fora de power não é afetado */
}
```

Vantagem: parâmetros funcionam como variáveis locais convenientemente inicializadas.

> [!tip] Para modificar o original
> Passar o **endereço** da variável (ponteiro). A função declara o parâmetro como ponteiro e acessa indiretamente. Ver Capítulo 5 do K&R.

### Arrays são exceção

Quando um array é passado, a função recebe o **endereço do primeiro elemento** — não uma cópia. A função pode modificar os elementos originais.

```c
void copy(char to[], char from[])   /* to e from são ponteiros */
{
    int i = 0;
    while ((to[i] = from[i]) != '\0')
        ++i;
}
```

---

## Tipo de retorno

```c
void copy(char to[], char from[]);   /* não retorna valor */
int getline(char s[], int lim);      /* retorna int */
```

- `void`: declara explicitamente que nenhum valor é retornado
- Se o tipo de retorno for omitido, ANSI C assume `int` (mas declarar explicitamente é boa prática)

---

## `main` e valor de retorno

`main` é uma função. Retorna status ao ambiente (SO):

```c
main()
{
    ...
    return 0;    /* 0 = terminação normal */
}
```

- `return 0` → terminação normal
- Valor não-zero → condição anormal ou erro

---

## Funções retornando não-inteiros (4.2)

Por padrão, função sem declaração explícita assume retorno `int`. Para outros tipos, declarar antes do uso:

```c
/* atof: converte string s para double */
double atof(char s[])
{
    double val, power;
    int i, sign;
    for (i = 0; isspace(s[i]); i++)
        ;
    sign = (s[i] == '-') ? -1 : 1;
    if (s[i] == '+' || s[i] == '-') i++;
    for (val = 0.0; isdigit(s[i]); i++)
        val = 10.0 * val + (s[i] - '0');
    if (s[i] == '.') i++;
    for (power = 1.0; isdigit(s[i]); i++) {
        val = 10.0 * val + (s[i] - '0');
        power *= 10;
    }
    return sign * val / power;
}
```

A rotina chamadora deve declarar o tipo de retorno:

```c
main()
{
    double sum, atof(char []);   /* declara atof retorna double */
    ...
}
```

> [!warning] Mismatch silencioso
> Se `atof` for compilada separadamente e a chamadora não a declarar, o compilador assume retorno `int`. O `double` retornado será tratado como `int` — resultado sem sentido, sem erro de compilação.
>
> Regra: se função não retorna `int`, declarar explicitamente antes do uso ou em header compartilhado.

`atof` com retorno correto permite escrever `atoi` em termos dela:

```c
int atoi(char s[])
{
    double atof(char s[]);
    return (int) atof(s);   /* cast explicita truncamento intencional */
}
```

---

## Funções definidas em múltiplos arquivos

Funções podem ser distribuídas em vários arquivos-fonte. Nenhuma função pode ser dividida entre arquivos. Para usar uma função de outro arquivo, seu protótipo deve estar acessível (tipicamente via header `.h`). Ver [[Pré-processador C]] para `#include`.

---

## Ver também
- [[Escopo em C]] — variáveis automáticas vs. externas; `extern`; arquivos de cabeçalho
- [[Arrays em C]] — arrays passados como ponteiro (não por valor)
- [[Linguagem C]] — estrutura geral; `main` como ponto de entrada
- [[Tipos de Dados C]] — tipos de retorno; `void`
- [[Pré-processador C]] — `#include`, headers compartilhados
