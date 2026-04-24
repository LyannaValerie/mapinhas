---
title: Organização e Documentação em C
aliases:
  - documentação de interface C
  - organização de programas C
  - interface em C
tags:
  - computação/fundamentos
source: Jens Gustedt, material didático sobre C23
date: 2026-04-23
created_by: Codex
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Programação]]

# Organização e Documentação em C

## Definição

Disciplina de separar interface, implementação e documentação em programas C. A linguagem não possui um mecanismo nativo de módulo no estilo de linguagens mais recentes; a organização depende de arquivos `.h`, arquivos `.c`, declarações, definições e convenções de build.

## Relações (SPO)
- Arquivo de cabeçalho → declara → interface pública
- Arquivo de implementação → define → comportamento
- `#include` → importa → declarações necessárias
- Documentação de interface → descreve → contrato de uso
- Implementação → deve preservar → contrato documentado

---

## Interface

Interface em C é o conjunto de nomes, tipos, funções, macros e constantes que outro arquivo pode usar. Normalmente fica em arquivo de cabeçalho:

```c
/* stack.h */
bool stack_push(struct stack *s, int value);
bool stack_pop(struct stack *s, int *out);
```

Boa interface informa:

- pré-condições
- pós-condições
- propriedade de memória
- possibilidade de falha
- significado de valores de retorno

## Implementação

Implementação deve ocultar detalhes que não fazem parte do contrato. Funções e objetos internos podem usar `static` para limitar visibilidade ao arquivo de tradução. Ver [[Escopo em C]] e [[Variáveis Estáticas em C]].

## Cabeçalhos

Cabeçalhos devem ser autocontidos: um arquivo que inclui o cabeçalho deve receber todos os tipos e declarações necessários. Guardas de inclusão pertencem a [[Pré-processador C]].

## Nota de integração

criado pelo Codex

