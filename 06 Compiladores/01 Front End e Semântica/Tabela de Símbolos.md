---
title: Tabela de Símbolos
tags:
  - compiladores
  - semântica
aliases:
  - symbol table
  - tabela de símbolos
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Compiladores]]

# Tabela de Símbolos

Repositório central onde o compilador armazena informações sobre **nomes** encontrados no programa: variáveis, constantes, procedimentos, funções, labels, structs.

> [!info] Contexto
> Seção 5.5 de *Engineering a Compiler*. Componente integral da [[Representação Intermediária (IR)]].

## Informações Armazenadas

Por nome, o compilador precisa de:

| Tipo de Entidade | Informações |
|---|---|
| Variável escalar | tipo, classe de armazenamento, nível léxico, procedimento declarante, base + offset |
| Array | tipo do elemento, número de dimensões, limites por dimensão |
| Struct/Record | lista de campos, tipo e offset de cada campo |
| Procedure/Function | número e tipos de parâmetros, tipo de retorno, convenção de chamada |

## Implementação: Hash Table

Lookup em tempo O(1) esperado. Função hash `h` mapeia nomes para inteiros pequenos usados como índice.

```
h(name) → índice
tabela[índice] → entrada com atributos do nome
```

### Colisões
Tratadas por encadeamento (lista ligada em cada slot) ou endereçamento aberto.

### Alternativa: Árvore Balanceada
- Lookup O(log n)
- Útil quando ordem lexicográfica importa (ex: relatórios de erro ordenados)

## Interface Básica

```
Insert(name, attributes)   -- insere nome com atributos
LookUp(name)               -- retorna atributos ou falha
```

- `Insert` detecta declarações duplicadas
- `LookUp` falha para nomes não declarados (viola declare-before-use)

## Escopos Aninhados

Para linguagens com escopos léxicos (Algol, Pascal, C):

- Cada escopo tem sua própria tabela (ou partição)
- Escopo interno pode **ocultar** declarações do escopo externo
- Nível léxico 0 = escopo mais externo (globais)

### Implementação com Pilha de Tabelas

```
ao entrar num bloco:  push nova tabela de escopo
ao sair de um bloco:  pop tabela (nomes locais saem de escopo)
LookUp: procura da tabela do topo até a base da pilha
```

### Coordenada Estática

Cada nome tem coordenada `(nível, offset)`:
- **nível**: profundidade do escopo declarante
- **offset**: posição dentro do registro de ativação daquele escopo

## Múltiplas Tabelas Especializadas

Um compilador pode manter tabelas separadas para:
- variáveis locais por procedimento
- tipos definidos pelo usuário
- labels de goto
- constantes literais

## Tabela de Símbolos vs. IR

Alternativa à tabela: armazenar atributos diretamente na AST (annotations por nó). Porém:
- navegar na AST para encontrar uma declaração é caro
- tabela de símbolos substitui busca por mapeamento computado em O(1)

## Relações

- [[Análise Sensível ao Contexto]] — tabela é construída durante esta fase
- [[Tradução Dirigida por Sintaxe — Ad Hoc]] — ações SDT chamam Insert/LookUp
- [[Sistemas de Tipos]] — tipos armazenados na tabela
- [[Abstração de Procedimento]] — tabela registra informações de cada procedimento
- [[Espaço de Nomes e Escopo Léxico]] — scoping define como a tabela é estruturada
- [[Representação Intermediária (IR)]] — tabela é parte integral da IR
