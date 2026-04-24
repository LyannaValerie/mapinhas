---
title: IR Linear — Código de Três Endereços
tags:
  - compiladores
  - ir
aliases:
  - linear IR
  - three-address code
  - código de três endereços
  - ILOC
  - stack-machine code
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Compiladores]]

# IR Linear — Código de Três Endereços

IRs que representam o programa como **sequência linear de operações**, semelhante a assembly de máquina abstrata.

> [!info] Contexto
> Seção 5.3 de *Engineering a Compiler*. Parte do cap. 5 sobre [[Representação Intermediária (IR)]].

## Motivação

- Código fonte é linear; código alvo é linear → IR linear é natural
- Impõe ordenação clara e útil sobre operações
- Expõe propriedades da máquina alvo que o compilador deve ver explicitamente

## Blocos Básicos em IR Linear

Fluxo de controle demarca **blocos básicos**:
- Bloco termina em desvio (*branch*) ou salto (*jump*)
- Bloco começa após um rótulo (*label*)
- Desvio condicional: *taken branch* (vai ao rótulo) vs. *fall-through* (próxima operação)

## Tipos de IR Linear

| Tipo | Modelo | Exemplo |
|---|---|---|
| **One-address** | máquina acumuladora/pilha | código de bytecode Java |
| **Two-address** | operações destrutivas | x86 assembly |
| **Three-address** | dois operandos + resultado distintos | ILOC, IR de compiladores RISC |

### Stack-Machine Code
- Código compacto; operandos implícitos no topo da pilha
- Útil quando tamanho da IR importa (ex: Java applets transmitidos em rede)
- JVM bytecode é o exemplo clássico

### Three-Address Code (TAC)
Forma dominante em compiladores modernos. Cada instrução: `resultado ← operando1 op operando2`

```iloc
loadI  @a  ⇒ r1
loadAO rarp, r1 ⇒ r2    // load a
loadI  @b  ⇒ r3
loadAO rarp, r3 ⇒ r4    // load b
mult   r4, r6   ⇒ r7
sub    r2, r7   ⇒ r8    // a - b×c
```

## ILOC — IR Concreta do Livro

ILOC é a three-address code usada como exemplo em *Engineering a Compiler*:
- Máquina RISC hipotética com registradores virtuais ilimitados
- Cada bloco termina com branch ou jump explícito (sem fall-through)
- Operações: `loadI`, `loadAO`, `add`, `sub`, `mult`, `cmp`, `cbr`, `jumpI`, etc.

### Operações de Controle
```iloc
comp   ra, rb  ⇒ cc1        // compara, resultado em condition code
cbr LT cc1     → L1, L2     // branch condicional
jumpI          → L3         // salto incondicional
```

## Operações Destrutivas vs. Não-Destrutivas

- **Destrutiva**: um dos operandos é sobrescrito com o resultado (modelo two-address)
- ILOC usa three-address: resultado sempre em registrador separado

## Construção do CFG a partir de IR Linear

1. Identificar líderes de bloco: primeiro op, destinos de branches, ops após branches
2. Cada bloco: do líder até o próximo líder (exclusive)
3. Adicionar aresta para cada possível destino de branch

## Comparação com IR Graphical

| Aspecto | Graphical (AST) | Linear (TAC) |
|---|---|---|
| Identificar array ref | fácil (nó explícito) | difícil (endereço calculado) |
| Otimizar endereçamento | difícil | fácil |
| Paralelismo implícito | parcial | via grafo de dependência |

## Relações

- [[Representação Intermediária (IR)]] — overview geral
- [[IR Graphical — Árvores e Grafos]] — alternativa baseada em grafo
- [[Forma SSA]] — extensão de three-address code para análise
- [[Back End do Compilador]] — consome IR linear e emite assembly real
- [[Forma de Código — Expressões]] — código shape para expressões em ILOC
