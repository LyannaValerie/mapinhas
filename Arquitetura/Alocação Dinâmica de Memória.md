---
title: Alocação Dinâmica de Memória
aliases:
  - dynamic memory allocation
  - alocador dinâmico
  - malloc
  - free
  - heap
  - fragmentação
  - fragmentation
  - lista livre
  - free list
  - implicit free list
  - explicit free list
  - segregated free list
  - boundary tag
  - coalescing
  - garbage collection
  - mark and sweep
  - memory leak
  - dangling pointer
tags:
  - computação/arquitetura
date: 2026-04-14
---

# Alocação Dinâmica de Memória

## Definição
Mecanismo que permite a programas requisitar e liberar blocos de memória em tempo de execução, de tamanho arbitrário e em ordem arbitrária. Implementado por um **alocador dinâmico** que gerencia a região de **heap** do processo.

## Relações (SPO)
- Alocador → gerencia → heap do processo
- `malloc` → requisita → bloco de tamanho n bytes, alinhado a 16 bytes (x86-64)
- `free` → libera → bloco previamente alocado
- Fragmentação → reduz → utilização do heap
- Garbage collector → libera → blocos inacessíveis automaticamente

---

## Interface

```c
#include <stdlib.h>

void *malloc(size_t size);   // retorna ponteiro alinhado, ou NULL se falhar
void  free(void *ptr);       // ptr deve ter sido retornado por malloc/calloc/realloc

void *calloc(size_t n, size_t size);  // aloca + zera
void *realloc(void *ptr, size_t size);
void *sbrk(intptr_t incr);   // estende/contrai o heap (usada internamente)
```

> [!note] Alinhamento
> `malloc` retorna ponteiros alinhados a **16 bytes** em sistemas x86-64 (suficiente para qualquer tipo C primitivo e vetores SSE/AVX básicos).

---

## Metas do Alocador

| Meta | Descrição |
|---|---|
| **Throughput** | Requisições por segundo; minimizar tempo por `malloc`/`free` |
| **Utilização** | Proporção do heap ocupada por dados úteis vs. overhead |

As metas competem: maximizar throughput pode aumentar fragmentação (reduz utilização).

---

## Fragmentação

### Fragmentação interna
Bloco alocado **maior** que o solicitado (ex: alinhamento, overhead de cabeçalho). Desperdício dentro do bloco.

### Fragmentação externa
Memória livre total suficiente para uma requisição, mas **não há bloco contíguo** livre suficientemente grande. Causa: alternância de alocações e liberações de tamanhos variados.

---

## Estrutura do Heap

O alocador organiza o heap como uma sequência de **blocos**. Cada bloco tem:
- **Cabeçalho (header):** tamanho do bloco + bit de alocação
- **Payload:** dados do usuário
- **Padding:** alinhamento

```
┌────────────┬──────────────────┬─────────┐
│  Header    │    Payload       │ Padding │
│ (4 bytes)  │   (n bytes)      │         │
└────────────┴──────────────────┴─────────┘
```

**Boundary tags (rodapé):** cópia do header no fim do bloco. Permite fusão de blocos livres adjacentes em O(1) em qualquer direção (*coalescing* bidirecional).

---

## Estratégias de Posicionamento (Placement)

| Estratégia | Descrição | Trade-off |
|---|---|---|
| **First fit** | Primeiro bloco livre suficientemente grande | Rápido; fragmentação no início |
| **Next fit** | Primeiro fit a partir da última busca | Mais rápido; fragmentação distribuída |
| **Best fit** | Menor bloco livre ≥ tamanho solicitado | Menor fragmentação; mais lento |

---

## Coalescing (Fusão de Blocos)

Ao liberar um bloco, fundi-lo com vizinhos livres imediatos:

- **Vizinho anterior livre:** possível via boundary tag (rodapé do bloco anterior)
- **Vizinho posterior livre:** via header do próximo bloco

Sem coalescing → fragmentação externa cresce indefinidamente com alocações/liberações mistas.

**Coalescing imediato** vs. **coalescing diferido** (agrupa fusões para eficiência).

---

## Organizações da Lista Livre

### Lista livre implícita

Todos os blocos (livres e alocados) formam uma lista ligada via tamanho no header.

- Alocação: percorre toda a lista → O(n) no número total de blocos
- Simples de implementar; inadequada para uso geral (lenta)

### Lista livre explícita

Blocos livres formam lista duplamente ligada. Ponteiros `prev`/`next` armazenados no payload do bloco livre (não usado enquanto livre).

- Alocação: percorre apenas blocos livres → mais rápido que implícita
- Liberação: O(1) para inserir na lista; coalescing ainda necessário

Políticas de ordenação:
- **LIFO:** bloco liberado vai para o início — simples, O(1)
- **Por endereço:** mantém lista ordenada por endereço — melhor utilização, mais lento

### Listas livres segregadas (*segregated free lists*)

Múltiplas listas, cada uma para uma classe de tamanho (ex: {1–8}, {9–16}, {17–32}, ..., {> 1024}).

Para alocar n bytes:
1. Encontra classe de tamanho para n
2. Busca bloco nessa lista (first fit)
3. Se não encontra, sobe para a próxima classe
4. Se nenhuma → `sbrk()` para estender o heap

Vantagens:
- Alocação e liberação aproximadamente O(1)
- Fragmentação reduzida (blocos de tamanho próximo agrupados)
- Utilizado por alocadores de produção (ptmalloc, jemalloc, tcmalloc)

### Buddy system

Espaço de tamanho 2ᵏ. Cada bloco tem um "buddy" de mesmo tamanho. Fusão fácil: só funde com buddy se ambos livres.

- Fragmentação interna alta (arredonda para potência de 2)
- Fusão extremamente eficiente

---

## Garbage Collection

**Garbage collector (GC):** alocador que libera automaticamente blocos inacessíveis (*garbage*). Programa chama `malloc` mas nunca `free`.

### Mark-and-sweep

Dois passos:

**Mark (marcação):**
- Raízes: variáveis globais, pilha, registradores que contêm ponteiros para o heap
- A partir de cada raiz, percorre grafo de apontadores recursivamente
- Marca cada bloco alcançável

**Sweep (varredura):**
- Percorre todos os blocos do heap
- Blocos não marcados → libera (garbage)

> [!warning] GC conservador em C
> C não distingue ponteiros de inteiros em tempo de execução. GC conservador trata qualquer palavra que *parece* ser um ponteiro para um bloco do heap como um ponteiro real. Blocos podem não ser coletados mesmo sendo inacessíveis (falsos positivos de ponteiro).

---

## Erros Comuns de Memória em C

### Ponteiro dangling (*dangling pointer*)
```c
int *p = malloc(sizeof(int));
free(p);
*p = 42;  // erro: p aponta para bloco liberado
```

### Leitura não inicializada
```c
int *p = malloc(sizeof(int));
printf("%d\n", *p);  // erro: malloc não zera; usar calloc
```

### Estouro de buffer (*heap buffer overrun*)
```c
int *p = malloc(10 * sizeof(int));
p[10] = 0;  // erro: acesso além do bloco
```

### Ponteiro deslocado (*off-by-one*)
```c
char *s = malloc(strlen(str));  // erro: falta 1 byte para '\0'
strcpy(s, str);
```

### Double free
```c
free(p);
free(p);  // erro: comportamento indefinido, corrompe heap
```

### Memory leak
```c
void f() {
    int *p = malloc(100 * sizeof(int));
    return;  // erro: p nunca liberado; heap cresce indefinidamente
}
```

### Referência a variável local liberada
```c
int *f() {
    int x = 42;
    return &x;  // erro: x na pilha; inválido após retorno
}
```

---

## Ver também
- [[Memória Virtual]] — heap é região do espaço de endereçamento virtual; `sbrk` ajusta seu limite
- [[Mapeamento de Memória]] — `malloc` usa `mmap` para blocos grandes (glibc: > ~128 KiB)
- [[Pilha]] — alternativa ao heap para alocação de duração de função
- [[Buffer Overflow]] — heap overrun como vetor de ataque
- [[Processo]] — heap é por processo; `sbrk` é system call do kernel
