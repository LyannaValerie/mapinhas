---
title: Região Crítica
aliases:
  - seção crítica
  - critical section
tags:
  - computação/sistemas
source: "Sistemas Operacionais Modernos, cap. 2"
created_by: Codex
---

# Região Crítica

Nota criada pelo Codex.

## Definição

Trecho de execução que acessa recurso ou dado compartilhado e precisa ser protegido contra acesso concorrente incompatível.

## Critério

Uma região crítica correta impede que duas entidades executem simultaneamente operações conflitantes sobre o mesmo estado compartilhado.

## Relações (SPO)

- Região Crítica → protege → estado compartilhado
- Região Crítica → requer → [[Exclusão Mútua]]
- Região Crítica → evita → [[Condição de Corrida]]
- [[Mutex]] → implementa → entrada controlada em Região Crítica
- [[Semáforo]] → pode proteger → Região Crítica

