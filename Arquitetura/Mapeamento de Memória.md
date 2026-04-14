---
title: Mapeamento de Memória
aliases:
  - memory mapping
  - mmap
  - copy-on-write
  - COW
  - objeto compartilhado
  - objeto privado
  - shared object
  - private object
  - área de memória virtual
  - vm_area_struct
tags:
  - computação/arquitetura
date: 2026-04-14
---

# Mapeamento de Memória

## Definição
Técnica em que o Linux inicializa o conteúdo de uma área de memória virtual associando-a a um **objeto em disco**. O objeto pode ser um arquivo regular ou um *anonymous file* criado pelo kernel.

Conjunto de áreas virtuais mapeadas de um processo = **mapa de memória** do processo.

## Relações (SPO)
- Mapeamento de Memória → associa → área virtual → objeto em disco
- `mmap()` → cria → novo mapeamento no espaço virtual do processo
- Copy-on-write → adia → cópia de páginas até escrita ocorrer
- `fork()` → usa → copy-on-write para criar espaço do filho
- `execve()` → usa → mapeamento de memória para carregar executável

---

## Tipos de Objeto Mapeado

| Tipo | Descrição |
|---|---|
| **Arquivo regular** | Arquivo em disco (executável, biblioteca, dado) |
| **Anonymous file** | Criado pelo kernel; páginas inicializadas com zeros (*demand-zero pages*) |

Páginas de um anonymous file nunca têm backing em disco real — se precisam ser paginadas, vão para o *swap file*.

---

## Objetos Compartilhados vs. Privados

### Objeto compartilhado (*shared object*)

Mapeado com flag `MAP_SHARED`. Escritas no espaço virtual **refletem no arquivo em disco** e são visíveis a outros processos que mapearem o mesmo objeto.

```
Processo A ──→ PP X ──→ arquivo em disco
Processo B ──→ PP X
```

Ambos apontam para a mesma PP. Escrita de A é vista por B.

### Objeto privado (*private object*) — Copy-on-Write

Mapeado com `MAP_PRIVATE`. Escritas são **privadas** — não refletem no disco nem em outros processos.

Implementação eficiente via **copy-on-write (COW)**:

1. Inicialmente, múltiplos processos mapeiam a mesma PP física (somente leitura)
2. PTE marcada como read-only + COW
3. Tentativa de escrita → exceção de proteção → kernel intercepta
4. Kernel **cria cópia privada** da PP para o processo que escreveu
5. PTE atualizada para a nova PP → escrita prossegue

> [!tip] COW é eficiente
> Páginas somente lidas nunca são copiadas — apenas páginas efetivamente modificadas geram cópia. Economiza memória e tempo de inicialização.

---

## `fork()` e Memória Virtual

Quando `fork()` cria processo filho:

1. Kernel cria estruturas do processo filho (cópia do descritor do pai)
2. **Não copia** as páginas físicas
3. Marca todas as PTEs de pai e filho como **read-only + COW**
4. Filho começa com mapa de memória idêntico ao pai (mesmas PPs)

Na primeira escrita (pai ou filho):
- Exceção de proteção → kernel cria cópia da página → PTE atualizada
- Demais páginas permanecem compartilhadas

Resultado: `fork()` é O(1) em páginas — custo proporcional às páginas efetivamente modificadas após o fork, não ao tamanho do processo.

---

## `execve()` e Memória Virtual

Para carregar e executar `a.out` no processo atual:

1. **Apaga** mapeamentos existentes do espaço de usuário
2. **Mapeia** segmentos do novo executável:
   - `.text`, `.rodata` → objeto privado, read-only, mapeado do arquivo ELF
   - `.data`, `.bss` → objeto privado, read-write; `.bss` = demand-zero (anonymous)
3. **Mapeia** área para pilha (anonymous, demand-zero)
4. **Mapeia** bibliotecas compartilhadas (se ainda não mapeadas)
5. Define `%rip` para o entry point

Nenhuma página é copiada para a RAM neste momento — tudo é demand paging. Páginas carregadas sob demanda via page fault na primeira execução.

---

## `mmap()` — Interface de Programação

```c
#include <sys/mman.h>

void *mmap(void *start, size_t length, int prot, int flags, int fd, off_t offset);
```

| Parâmetro | Descrição |
|---|---|
| `start` | Endereço sugerido (geralmente `NULL` → kernel escolhe) |
| `length` | Tamanho da área |
| `prot` | Proteção: `PROT_READ`, `PROT_WRITE`, `PROT_EXEC`, `PROT_NONE` |
| `flags` | `MAP_SHARED` ou `MAP_PRIVATE`; `MAP_ANON` para anonymous |
| `fd` | Descritor do arquivo (−1 se `MAP_ANON`) |
| `offset` | Offset no arquivo (múltiplo de tamanho de página) |

Retorna: endereço inicial da área mapeada, ou `MAP_FAILED` em erro.

### `munmap()` — remover mapeamento

```c
int munmap(void *start, size_t length);
```

---

## Usos Práticos

| Uso | Flags típicas |
|---|---|
| Ler arquivo grande sem `read()` | `MAP_SHARED`, `PROT_READ` |
| Comunicação entre processos (IPC) | `MAP_SHARED`, `PROT_READ\|PROT_WRITE` |
| Carregar executável / biblioteca | `MAP_PRIVATE` (COW) |
| Heap com `malloc` (blocos grandes) | `MAP_ANON`, `MAP_PRIVATE` |

---

## Ver também
- [[Memória Virtual]] — base: páginas virtuais, tabela de páginas, page fault
- [[Processo]] — `fork()`, `execve()`, espaço de endereçamento
- [[Formato ELF]] — segmentos `.text`, `.data`, `.bss` mapeados por `execve()`
- [[PIC e GOT]] — bibliotecas compartilhadas usam mapeamento de objetos compartilhados
- [[Alocação Dinâmica de Memória]] — `malloc` usa `mmap` para blocos grandes e `sbrk` para o heap
- [[Fluxo de Controle Excepcional]] — page fault é exceção tratada pelo kernel
