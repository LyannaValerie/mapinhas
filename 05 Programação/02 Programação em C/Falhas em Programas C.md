---
title: Falhas em Programas C
aliases:
  - erro em C
  - program failure C
  - cleanup em C
tags:
  - computação/fundamentos
source: Jens Gustedt, material didático sobre C23
date: 2026-04-23
created_by: Codex
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Programação]]

# Falhas em Programas C

## Definição

Conjunto de condições em que programa C deixa de cumprir contrato: erro de entrada, falha de alocação, estado corrompido, violação de pré-condição, erro de sistema ou comportamento indefinido.

## Relações (SPO)
- Falha de alocação → retorna → ponteiro nulo
- Erro de biblioteca → pode sinalizar → valor especial ou `errno`
- Estado degradado → exige → checagem e limpeza
- `assert` → documenta → invariantes de desenvolvimento

---

## Classes de falha

| Classe | Exemplo |
|---|---|
| uso incorreto | argumento inválido |
| recurso indisponível | `malloc` falha |
| entrada inválida | formato inesperado em `scanf` |
| estado inconsistente | estrutura interna quebrada |
| ambiente externo | arquivo ausente, permissão negada |

## Tratamento

Tratamento robusto usa um caminho claro de saída:

- validar argumentos na fronteira
- checar retornos de biblioteca
- preservar invariantes
- liberar recursos adquiridos parcialmente
- reportar erro no nível certo

## Relação com `assert`

`assert` pertence a invariantes internas e erros de programação. Não substitui validação de entrada externa. Ver [[Biblioteca assert.h]].

## Nota de integração

criado pelo Codex

