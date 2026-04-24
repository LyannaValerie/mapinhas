---
title: Passagem de Parâmetros
tags:
  - compiladores
  - procedimentos
aliases:
  - parameter passing
  - call by value
  - call by reference
  - passagem de parâmetros
  - parameter binding
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Compiladores]]

# Passagem de Parâmetros

**Parameter binding** mapeia **parâmetros atuais** (no call site) para **parâmetros formais** (no callee).

> [!info] Contexto
> Seção 6.4.1 de *Engineering a Compiler*. Parte do cap. 6 sobre [[Abstração de Procedimento]].

## Call by Value (Chamada por Valor)

- Caller avalia cada parâmetro atual e passa o **valor** ao callee
- Callee recebe cópia; modificações **não são visíveis** no caller
- Implementação: valor copiado para slot no AR do callee

```c
int fee(int x, int y) {
  x = 2 * x;     // não afeta variável no caller
  y = x + y;
  return y;
}
int a = 2, b = 3;
c = fee(a, b);   // a e b não mudam
```

## Call by Reference (Chamada por Referência)

- Caller passa o **endereço** do parâmetro atual
- Callee acessa e modifica via ponteiro → **modificações são visíveis** no caller
- Implementação: endereço (ponteiro) copiado para slot no AR do callee

```pascal
procedure swap(var x, y: integer);
// x e y são ponteiros implícitos para as variáveis do caller
```

> [!warning] Aliasing
> `fee(a, a)` com call-by-reference faz `x` e `y` apontarem para o mesmo local — comportamento inesperado se a função assume independência.

## Call by Value-Result

- Copia o valor na entrada (como call-by-value) e copia de volta na saída
- Evita aliasing durante a execução
- Semanticamente diferente de call-by-reference quando aliasing ocorre

## Call by Name (Chamada por Nome)

Introduzido pelo Algol 60. A referência a um parâmetro formal se comporta como se o **parâmetro atual fosse textualmente substituído**.

- Implementação: **thunks** — funções que avaliam o parâmetro atual e retornam um ponteiro
- Problemas: thunks são caros de gerar e avaliar; comportamento surpreendente
- Praticamente abandonado em linguagens modernas

### Exemplo Clássico (Algol 60)
```algol
procedure zero(Arr, i, j, u1, u2);
  for i := 1 until u1 do
    for j := 1 until u2 do
      Arr := 0;
zero(Work[p,q], p, q, 100, 200);
// substitui Arr por Work[p,q] → zera toda a matriz Work
```

## Retorno de Valores

- Valores simples: em registrador dedicado (ex: RAX em x86-64 ABI)
- Structs grandes: caller aloca espaço e passa ponteiro implícito ao callee

## Endereçabilidade (Addressability)

Em compilações separadas, o compilador não conhece o endereço de todos os símbolos. Usa:
- **Endereçamento relativo ao PC**: endereços resolvidos pelo linker
- **GOT (Global Offset Table)**: para símbolos dinâmicos em shared libraries

## Relações

- [[Abstração de Procedimento]] — contexto geral de chamadas
- [[Espaço de Nomes e Escopo Léxico]] — parâmetros habitam o escopo do callee
- [[Gerenciamento de Heap]] — objetos passados por referência podem viver no heap
- [[Forma de Código — Expressões]] — geração de código para avaliação de parâmetros
