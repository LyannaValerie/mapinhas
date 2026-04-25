---
title: Funções Inline em C
aliases:
  - inline C
  - função inline C
  - inlining em C
tags:
  - computação/programação/c
source: Jens Gustedt, Modern C, seção 16.1
date: 2026-04-24
created_by: Codex
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Programação]]

# Funções Inline em C

## Definição

`inline` torna a definição da função visível ao compilador no ponto de uso. Isso pode eliminar overhead de chamada e abrir otimizações locais, mas não obriga expansão.

## Relações (SPO)
- `inline` → expõe → definição da função
- inlining → remove → overhead de chamada
- definição visível → permite → otimização contextual
- função inline pública → exige → interface estável
- identificador local → pode sofrer → expansão de macro

---

## Uso

`inline` favorece funções pequenas, estáveis e chamadas em caminhos quentes. O ganho não é só remover chamada: depois da expansão, o compilador enxerga constantes, ramos mortos e recomputações evitáveis no contexto do chamador.

## Restrições

Função inline exposta em header deve ser tratada como parte da interface. Seus identificadores locais também ficam textualmente próximos de macros incluídas pelo usuário, então nomes internos precisam de convenção que reduza colisão.

Função inline não tem uma unidade de tradução única associada. Por isso, não deve depender diretamente de funções `static` ou objetos `static` modificáveis por nome.

## Ver também

- [[Funções em C]]
- [[Macros Tipo Função em C]]
- [[Performance em C]]
- [[Pré-processador C]]

## Nota de integração

criado pelo Codex

