---
title: Espaço de Nomes e Escopo Léxico
tags:
  - compiladores
  - procedimentos
  - escopo
aliases:
  - name space
  - lexical scope
  - escopo léxico
  - static scope
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Compiladores]]

# Espaço de Nomes e Escopo Léxico

Define **quais nomes são visíveis** em cada ponto do programa e a qual declaração cada uso se refere.

> [!info] Contexto
> Seção 6.3 de *Engineering a Compiler*. Parte do cap. 6 sobre [[Abstração de Procedimento]].

## Escopo Léxico (Estático)

Em **escopo léxico**, um nome refere-se à declaração **lexicamente mais próxima** — a declaração no escopo envolvente mais interno.

> Princípio: em um dado escopo, cada nome refere-se à sua declaração lexicamente mais próxima.

- Escopos se **aninham**: cada novo procedimento/bloco cria um escopo
- Escopo interno pode **ocultar** declarações de escopo externo com o mesmo nome

## Linguagens Algol-like (ALLs)

Pascal, Ada, Modula — herdam convenções do Algol 60:
- Escopos demarcados por `begin/end` ou `{}`
- Procedimentos aninhados permitidos (Pascal)
- Escopos globais no nível 0

### Coordenada Estática

Cada nome `v` declarado em nível `l`, offset `o` → coordenada `(l, o)`.

Para acessar uma variável no nível `l'` a partir do nível `l` (`l' < l`): seguir `l - l'` **links de acesso** (access links / static links).

```
AR corrente (nível l):
  link de acesso → AR do escopo pai (nível l-1)
                    link de acesso → AR do avô (nível l-2)
                                     ...
```

### Display

Alternativa ao encadeamento de links de acesso: array `display` onde `display[i]` aponta para o AR ativo do nível `i`. Acesso a variável de nível `i` = um load de `display[i]` + offset.

## Linguagens Orientadas a Objetos

- Não têm procedimentos aninhados em geral
- Hierarquia de classes define visibilidade (herança)
- `this`/`self` = referência implícita ao objeto receptor

### Object Record (OR) Layout

Com **herança simples**:
- Campos da superclasse vêm primeiro, no mesmo offset
- Subclasse adiciona campos ao final
- Garante compatibilidade: ponteiro para subclasse funciona onde superclasse é esperada

Com **herança múltipla**:
- Layout mais complexo; pode exigir ajuste de ponteiro na conversão de tipo
- C++ usa delta thunks ou tabelas de vtable por base

## Escopo Dinâmico

Alternativa ao léxico: nome refere-se à declaração **mais recente na pilha de chamadas**. Mais difícil de raciocinar; usado em Lisp antigo, alguns sistemas de macros.

## Relações

- [[Abstração de Procedimento]] — escopo define o que cada AR contém
- [[Tabela de Símbolos]] — tabela estruturada por escopos léxicos
- [[Passagem de Parâmetros]] — parâmetros habitam o espaço de nomes do callee
- [[Sistemas de Tipos]] — tipo de um nome é resolvido via escopo
