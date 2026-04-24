---
title: Threads em C
aliases:
  - threads C
  - thrd_t
  - programação concorrente C
tags:
  - computação/fundamentos
source: Jens Gustedt, material didático sobre C23
date: 2026-04-23
created_by: Codex
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Programação]]

# Threads em C

## Definição

Execuções concorrentes dentro do mesmo processo, compartilhando espaço de endereçamento. C moderno define uma biblioteca de threads com criação, sincronização, dados locais e primitivas de coordenação.

## Relações (SPO)
- Thread → compartilha → memória do processo
- Mutex → protege → seção crítica
- Variável de condição → coordena → espera por estado
- Dado thread-local → cria → instância por thread
- Liveness → depende de → ausência de deadlock e starvation

---

## Controle entre threads

Concorrência correta exige declarar quais dados são compartilhados e quais invariantes protegem esse compartilhamento. Acesso simultâneo sem sincronização adequada pode produzir disputa de dados.

## Seções críticas

Seção crítica é região que acessa estado compartilhado protegido:

```c
mtx_lock(&m);
/* acessa estado compartilhado */
mtx_unlock(&m);
```

## Variáveis de condição

Variáveis de condição permitem bloquear uma thread até que outra sinalize mudança de estado. O predicado deve ser rechecado após acordar.

## Ver também

- [[Atomicidade e Consistência de Memória em C]]

## Nota de integração

criado pelo Codex

