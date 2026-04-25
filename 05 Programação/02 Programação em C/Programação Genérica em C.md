---
title: Programação Genérica em C
aliases:
  - generic programming C
  - _Generic C
  - seleção genérica C
tags:
  - computação/fundamentos
source: Jens Gustedt, material didático sobre C23
date: 2026-04-23
created_by: Codex
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Programação]]

# Programação Genérica em C

## Definição

Técnicas para escrever código que opera sobre múltiplos tipos sem duplicação manual excessiva. Em C moderno, inclui recursos inerentemente genéricos, macros e seleção genérica com `_Generic`.

## Relações (SPO)
- `_Generic` → seleciona → expressão por tipo
- Macro → fornece → interface sintática genérica
- Função real → preserva → checagem de tipo e avaliação única
- Tipo inferido → reduz → redundância em declarações

---

## Seleção genérica

`_Generic` escolhe implementação conforme tipo da expressão controladora:

```c
#define abs_generic(x) _Generic((x), \
    int: abs, \
    long: labs, \
    double: fabs \
)(x)
```

## Padrão recomendado

Combinar macro fina com funções reais:

- macro decide tipo
- função executa operação
- argumentos são avaliados uma vez
- erros de tipo aparecem cedo

## Relação com macros

Programação genérica em C usa [[Macros Tipo Função em C]], mas não deve herdar suas armadilhas sem controle.

Interface genérica robusta combina macro fina para seleção, função real para execução e [[Funções Inline em C]] quando a chamada pequena precisa permanecer barata.

## Ver também

- [[Macros Tipo Função em C]]
- [[Funções Inline em C]]
- [[Restrict em C]]
- [[Performance em C]]

## Nota de integração

criado pelo Codex
