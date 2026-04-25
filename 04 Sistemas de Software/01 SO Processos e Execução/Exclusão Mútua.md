---
title: Exclusão Mútua
aliases:
  - mutual exclusion
  - mutex discipline
tags:
  - computação/sistemas
source: "Sistemas Operacionais Modernos, cap. 2"
created_by: Codex
---

# Exclusão Mútua

Nota criada pelo Codex.

## Definição

Propriedade que garante que no máximo uma entidade execute uma [[Região Crítica]] sobre certo recurso compartilhado em um dado instante.

## Papel técnico

Exclusão mútua é mecanismo central para impedir [[Condição de Corrida]]. Ela não elimina concorrência do sistema inteiro; limita apenas o acesso simultâneo ao trecho que manipula estado compartilhado.

## Relações (SPO)

- Exclusão Mútua → protege → [[Região Crítica]]
- Exclusão Mútua → evita → [[Condição de Corrida]]
- [[Mutex]] → implementa → Exclusão Mútua
- [[Semáforo]] → pode implementar → Exclusão Mútua
- [[Escalonamento de CPU]] → influencia → ordem de entrada na Região Crítica

