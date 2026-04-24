---
title: Abstração de Procedimento
tags:
  - compiladores
  - procedimentos
  - linkagem
aliases:
  - procedure abstraction
  - procedure call
  - linkage convention
  - convenção de chamada
  - activation record
  - registro de ativação
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Compiladores]]

# Abstração de Procedimento

O procedimento é a **abstração de controle central** das linguagens modernas. Fornece ambiente de execução controlado com armazenamento nomeado privado.

> [!info] Contexto
> Cap. 6 de *Engineering a Compiler*.

## Três Abstrações do Procedimento

| Abstração | Descrição |
|---|---|
| **Chamada de procedimento** | mecanismo de invocação, mapeamento de argumentos, retorno de controle ao ponto de chamada |
| **Espaço de nomes** | cada procedimento tem seu espaço privado; interação com outros via parâmetros e variáveis globais |
| **Compilação separada** | procedimentos compilados independentemente, linkeditados depois |

## Terminologia

- **Caller**: procedimento que invoca
- **Callee**: procedimento invocado
- **Parâmetros formais**: nomes usados internamente pelo callee
- **Parâmetros atuais (reais)**: valores passados pelo caller

## Registro de Ativação (AR)

Estrutura alocada na pilha de execução para cada invocação de procedimento. Contém:

```
┌─────────────────────┐
│ parâmetros atuais   │  ← área de comunicação caller→callee
├─────────────────────┤
│ endereço de retorno │
├─────────────────────┤
│ link de controle    │  ← AR do caller
├─────────────────────┤
│ link de acesso      │  ← AR do escopo léxico pai
├─────────────────────┤
│ variáveis locais    │
├─────────────────────┤
│ temporários         │
└─────────────────────┘
```

- **ARP (Activation Record Pointer)** = registrador que aponta para o AR corrente

## Sequência de Chamada (Calling Sequence)

Dividida entre caller e callee:

**Prólogo (caller + callee)**:
1. Caller avalia parâmetros atuais
2. Caller salva estado (registradores caller-saves)
3. Caller transfere controle (`call`)
4. Callee salva registradores callee-saves
5. Callee aloca espaço para locais e temporários

**Epílogo (callee + caller)**:
1. Callee coloca valor de retorno em local acordado
2. Callee restaura registradores callee-saves
3. Callee retorna controle (`return`)
4. Caller restaura registradores caller-saves
5. Caller usa valor de retorno

## Convenções de Linkagem Padronizadas

Permitem **compilação separada**: procedimentos compilados por compiladores distintos interoperam se seguem a mesma ABI (Application Binary Interface).

Ex.: System V AMD64 ABI — parâmetros em RDI, RSI, RDX, RCX, R8, R9; retorno em RAX.

## Tamanho do AR

- **Fixo em compile time**: para linguagens sem recursão ou com tamanhos fixos de arrays
- **Variável em runtime**: linguagens com arrays de tamanho dinâmico (VLAs em C99)

## Objetos Orientados a Objetos

Em OO, o método é invocado relativo a um **objeto receptor** (*receiver*). O compilador passa implicitamente um ponteiro para o receptor (`this`/`self`) como primeiro parâmetro.

Layout do **objeto** (OR — Object Record):
- Campos de instância: offset fixo por campo
- Ponteiro para **method vector** (vtable): um por classe, contém ponteiros para métodos

Com herança, subclasse estende o layout da superclasse para garantir compatibilidade de offsets.

## Heap vs. Pilha

- **Pilha**: ARs de procedimentos com tempo de vida limitado à duração da chamada
- **Heap**: objetos com tempo de vida arbitrário (ver [[Gerenciamento de Heap]])

## Relações

- [[Espaço de Nomes e Escopo Léxico]] — como nomes são resolvidos dentro de procedimentos
- [[Passagem de Parâmetros]] — mecanismos de binding caller→callee
- [[Gerenciamento de Heap]] — alocação além da pilha
- [[Tabela de Símbolos]] — armazena informações sobre cada procedimento
- [[Forma de Código — Expressões]] — código shape para chamadas de função em expressões
