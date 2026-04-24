---
title: Estilo em C
aliases:
  - estilo de código C
  - formatação C
  - nomenclatura C
tags:
  - computação/fundamentos
source: Jens Gustedt, material didático sobre C23
date: 2026-04-23
created_by: Codex
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Programação]]

# Estilo em C

## Definição

Conjunto de convenções que torna código C legível, auditável e menos ambíguo. Estilo não muda a semântica da linguagem, mas reduz erro humano em programas que dependem de [[Ponteiros em C]], [[Arrays em C]], [[Structs em C]] e [[Pré-processador C]].

## Relações (SPO)
- Estilo em C → organiza → formatação, nomes e documentação local
- Nomenclatura consistente → reduz → colisões e leitura ambígua
- Formatação previsível → expõe → blocos, escopos e fluxos de controle
- Internacionalização → afeta → texto, caracteres e interface externa

---

## Formatação

Formatação deve tornar estrutura sintática visível:

- um bloco por nível de indentação
- chaves usadas de modo consistente
- expressões longas quebradas em pontos semânticos
- alinhamento limitado a casos em que ajuda comparação local

Em C, formatação ruim esconde bugs porque `if`, `for`, `while` e macros podem mudar comportamento com pequenas diferenças de escopo. Ver [[Controle de Fluxo C]].

## Nomes

Nomes devem indicar papel técnico:

| Tipo de nome | Boa prática |
|---|---|
| Variável local | curta quando escopo é curto |
| Função | verbo ou operação observável |
| Tipo | substantivo técnico |
| Macro | forma destacada, geralmente maiúscula |
| Identificador exportado | prefixo de módulo para evitar colisão |

## Internacionalização

C historicamente nasceu em ambiente ASCII, mas programas modernos lidam com múltiplos conjuntos de caracteres. Estilo de código deve separar:

- identificadores internos
- strings de interface
- codificação de entrada e saída
- comentários voltados a manutenção

## Nota de integração

criado pelo Codex

