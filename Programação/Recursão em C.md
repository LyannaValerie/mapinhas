---
title: Recursão em C
aliases:
  - recursão C
  - função recursiva C
  - recursividade C
tags:
  - computação/fundamentos
date: 2026-04-21
---

# Recursão em C

## Definição

Função que chama a si mesma, direta ou indiretamente. Cada chamada recebe um conjunto novo e independente de variáveis automáticas.

## Relações (SPO)
- Função recursiva → cria → novo frame de pilha a cada chamada
- Variáveis automáticas → são → independentes entre chamadas recursivas
- Recursão → não economiza → memória nem tempo em geral
- Recursão → vantagem quando → estrutura do problema é recursiva (árvores, quicksort)

---

## Exemplo: printd

Imprimir inteiro como string: dígitos gerados em ordem inversa (low-order primeiro), mas devem ser impressos do mais significativo ao menos.

```c
#include <stdio.h>

/* printd: imprime n em decimal */
void printd(int n)
{
    if (n < 0) {
        putchar('-');
        n = -n;
    }
    if (n / 10)
        printd(n / 10);   /* chama a si mesmo com quociente */
    putchar(n % 10 + '0');
}
```

Execução de `printd(123)`:
1. `printd(123)` → chama `printd(12)`
2. `printd(12)` → chama `printd(1)`
3. `printd(1)` → imprime `'1'`, retorna
4. `printd(12)` → imprime `'2'`, retorna
5. `printd(123)` → imprime `'3'`, termina

---

## Exemplo: quicksort

Particiona array ao redor de um elemento pivô, aplica recursão em cada metade:

```c
/* qsort: ordena v[left]...v[right] em ordem crescente */
void qsort(int v[], int left, int right)
{
    int i, last;
    void swap(int v[], int i, int j);

    if (left >= right)     /* base: menos de 2 elementos */
        return;
    swap(v, left, (left + right)/2);   /* pivô para v[left] */
    last = left;
    for (i = left + 1; i <= right; i++)
        if (v[i] < v[left])
            swap(v, ++last, i);
    swap(v, left, last);               /* restaura pivô */
    qsort(v, left, last-1);
    qsort(v, last+1, right);
}

void swap(int v[], int i, int j)
{
    int temp;
    temp = v[i];
    v[i] = v[j];
    v[j] = temp;
}
```

A biblioteca padrão fornece `qsort` que aceita qualquer tipo.

---

## Trade-offs

| Aspecto | Recursão | Iteração |
|---|---|---|
| Memória | Usa pilha por chamada | Geralmente menor |
| Velocidade | Overhead de chamada | Geralmente maior |
| Clareza | Alta para problemas naturalmente recursivos | Alta para loops simples |
| Indicada para | Árvores, divisão-e-conquista | Processamento linear |

> [!info]
> Recursão é especialmente conveniente para estruturas de dados recursivas como árvores (ver Seção 6.6 do K&R).

---

## Ver também
- [[Funções em C]] — chamada de função, passagem por valor
- [[Controle de Fluxo C]] — alternativas iterativas
- [[Structs em C]] — estruturas auto-referenciais (listas, árvores)
