---
title: Ponteiro void em C
aliases:
  - void pointer C
  - void* C
  - ponteiro genérico C
tags:
  - computação/programação/c
source: Jens Gustedt, Modern C, capítulo 12
date: 2026-04-24
created_by: Codex
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Programação]]

# Ponteiro void em C

## Definição

`void *` é ponteiro para objeto de tipo não especificado. Ele preserva endereço de objeto, mas perde informação estática sobre o tipo apontado.

## Relações (SPO)
- qualquer ponteiro de objeto → converte para → `void *`
- `void *` → converte de volta para → tipo original
- `void *` → não pode ser → derreferenciado diretamente
- interface genérica → usa → `void *` + tamanho do elemento
- conversão errada de volta → pode causar → violação de tipo efetivo

---

## Uso típico

```c
void *raw = malloc(n * sizeof(int));
int *xs = raw;
```

Bibliotecas genéricas usam `void *` quando precisam receber objetos de qualquer tipo. Exemplo: `qsort` recebe base como `void *`, quantidade de elementos, tamanho de cada elemento e função de comparação.

## Limite

`void *` não carrega metadados de tamanho nem tipo. Quem recebe o ponteiro deve conhecer o contrato externo: tipo real, alinhamento, quantidade de elementos e tempo de vida.

## Ver também

- [[Modelo de Memória em C]]
- [[Tipos Efetivos em C]]
- [[Alinhamento em C]]
- [[Ponteiros para Função em C]]

## Nota de integração

criado pelo Codex
