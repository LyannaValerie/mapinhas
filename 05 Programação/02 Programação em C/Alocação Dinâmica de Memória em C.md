---
title: Alocação Dinâmica de Memória em C
aliases:
  - malloc C
  - free C
  - calloc C
  - alocação dinâmica C
  - heap C
  - sbrk
  - first fit C
tags:
  - computação/fundamentos
date: 2026-04-21
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Programação]]

# Alocação Dinâmica de Memória em C

## Definição

Gerenciamento de blocos de memória em tempo de execução. `malloc` aloca; `free` libera. Internamente mantidos em lista circular de blocos livres. Solicita mais memória ao SO via `sbrk` quando a lista livre esgota.

## Relações (SPO)
- `malloc(n)` → retorna → `void *` para `n` bytes não inicializados, ou `NULL`
- `calloc(n, size)` → retorna → `void *` para `n × size` bytes zerados, ou `NULL`
- `free(p)` → devolve → bloco à lista livre (`p` deve vir de `malloc`/`calloc`)
- `sbrk(n)` → solicita → `n` bytes adicionais ao SO (retorna `-1`, não `NULL`, em falha)
- Lista livre → é → circular, ordenada por endereço crescente

---

## Interface padrão

```c
#include <stdlib.h>

void *malloc(size_t n);
void *calloc(size_t n, size_t size);
void  free(void *p);
```

```c
int *ip = (int *) calloc(n, sizeof(int));   /* cast necessário */
```

---

## Algoritmo: first fit

1. Lista livre = lista circular de blocos com header
2. `malloc` percorre a partir do último bloco alocado (`freep`) — mantém lista homogênea
3. Primeiro bloco ≥ tamanho pedido → retornado inteiro ou dividido (tail end)
4. Nenhum bloco serve → `morecore` pede mais ao SO e insere na lista
5. `free` insere bloco na posição correta (por endereço) e funde com vizinhos adjacentes

---

## Estrutura do header

```c
typedef long Align;    /* tipo mais restritivo — força alinhamento */

union header {
    struct {
        union header *ptr;   /* próximo bloco na lista livre */
        unsigned size;       /* tamanho em unidades de header */
    } s;
    Align x;                 /* força alinhamento — nunca usado diretamente */
};
typedef union header Header;
```

`union` garante que cada bloco seja múltiplo do alinhamento mais restritivo da máquina. `Align` isola a dependência de arquitetura em um único typedef.

`malloc` retorna ponteiro **após** o header — o código do usuário não vê a estrutura de controle.

---

## morecore e sbrk

```c
#define NALLOC 1024   /* unidades mínimas por requisição ao SO */

static Header *morecore(unsigned nu)
{
    char *cp, *sbrk(int);
    Header *up;
    if (nu < NALLOC) nu = NALLOC;
    cp = sbrk(nu * sizeof(Header));
    if (cp == (char *) -1)   /* sbrk retorna -1 em erro, não NULL */
        return NULL;
    up = (Header *) cp;
    up->s.size = nu;
    free((void *)(up + 1));  /* insere na lista livre */
    return freep;
}
```

`sbrk` retorna `(char *) -1` em falha — não `NULL`. Cast necessário para comparação portável.

---

## free — fusão de blocos

```c
void free(void *ap)
{
    Header *bp, *p;
    bp = (Header *)ap - 1;   /* recua para o header do bloco */
    for (p = freep; !(bp > p && bp < p->s.ptr); p = p->s.ptr)
        if (p >= p->s.ptr && (bp > p || bp < p->s.ptr))
            break;
    if (bp + bp->s.size == p->s.ptr) {   /* funde com vizinho superior */
        bp->s.size += p->s.ptr->s.size;
        bp->s.ptr   = p->s.ptr->s.ptr;
    } else
        bp->s.ptr = p->s.ptr;
    if (p + p->s.size == bp) {           /* funde com vizinho inferior */
        p->s.size += bp->s.size;
        p->s.ptr   = bp->s.ptr;
    } else
        p->s.ptr = bp;
    freep = p;
}
```

Fusão de blocos adjacentes evita fragmentação progressiva da heap.

---

## Erros comuns

```c
/* ERRADO: acessa p->next após liberar p */
for (p = head; p != NULL; p = p->next)
    free(p);

/* CORRETO: salva next antes de liberar */
for (p = head; p != NULL; p = q) {
    q = p->next;
    free(p);
}
```

> [!danger] Use-after-free e double-free
> Acessar memória após `free` é comportamento indefinido. Liberar ponteiro não originado de `malloc`/`calloc` também. Ambos causam corrupção silenciosa da lista livre.

---

## Portabilidade

`malloc` compara ponteiros de blocos retornados por chamadas diferentes de `sbrk` — não garantido pelo padrão C (permite comparação somente dentro do mesmo array). Portável somente em arquiteturas com modelo de memória plano.

`typedef` e `union` isolam dependências de máquina (tamanho de `Align`, alinhamento) a pontos mínimos e identificáveis.

---

## Ver também
- [[Ponteiros em C]] — `void *`, aritmética de ponteiros, cast
- [[Structs em C]] — lista encadeada, campo `ptr`
- [[Unions em C]] — union para forçar alinhamento
- [[Typedef em C]] — `Header`, `Align`
- [[Descritores de Arquivo UNIX]] — `sbrk` como syscall UNIX
