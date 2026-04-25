---
title: Camadas do Software de E/S
aliases:
  - camadas de E/S
  - pilha de E/S
  - I/O software layers
tags:
  - computação/sistemas
source: "Sistemas Operacionais Modernos, cap. 5"
created_by: Codex
---

# Camadas do Software de E/S

Conteúdo criado pelo Codex.

## Definição

**Camadas do software de E/S** são a organização do subsistema de E/S em níveis com responsabilidades separadas e interfaces definidas.

## Camadas típicas

- software de E/S no espaço do usuário
- [[Software de E-S Independente de Dispositivo|Software de E/S Independente de Dispositivo]]
- [[Driver]]
- [[Tratador de Interrupção]]
- hardware de E/S

## Relações (SPO)

- Camadas do Software de E/S -> separam -> política e mecanismo
- Camadas do Software de E/S -> reduzem -> dependência de hardware específico
- [[Driver]] -> fica acima -> [[Tratador de Interrupção]]
- [[Software de E-S Independente de Dispositivo|Software de E/S Independente de Dispositivo]] -> fica acima -> [[Driver]]
