---
title: Mutex
aliases:
  - lock
  - trava
  - mutual exclusion lock
tags:
  - computação/sistemas
source: "Sistemas Operacionais Modernos, cap. 2"
created_by: Codex
---

# Mutex

Nota criada pelo Codex.

## Definição

Primitiva de sincronização usada para permitir que apenas uma entidade por vez entre em uma [[Região Crítica]].

## Uso

Uma thread adquire o mutex antes de acessar estado compartilhado e libera o mutex ao sair. Se outra thread tenta adquirir o mutex enquanto ele está ocupado, ela deve esperar ou bloquear conforme a implementação.

## Relações (SPO)

- Mutex → implementa → [[Exclusão Mútua]]
- Mutex → protege → estado compartilhado
- Mutex → sincroniza → [[Thread|threads]]
- Mutex → pode bloquear → execução concorrente
- Uso incorreto de Mutex → pode causar → deadlock

