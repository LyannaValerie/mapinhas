---
title: Forma de Código — Expressões
tags:
  - compiladores
  - geração-de-código
  - code-shape
aliases:
  - code shape
  - forma de código
  - treewalk code generator
  - geração de código para expressões
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Compiladores]]

# Forma de Código — Expressões

**Code shape**: o conjunto de todas as decisões do compilador sobre como representar uma computação em IR e em assembly. Afeta diretamente a qualidade do código final e a capacidade do otimizador.

> [!info] Contexto
> Cap. 7 de *Engineering a Compiler*. Foca na tradução de construtos da linguagem fonte.

## Treewalk Code Generator

Estratégia básica para expressões: percorrer a AST recursivamente.

```
expr(node):
  case BINOP:
    t1 ← expr(left(node))
    t2 ← expr(right(node))
    result ← NextRegister()
    emit(op(node), t1, t2, result)
  case IDENT:
    t1 ← base(node); t2 ← offset(node)
    result ← NextRegister()
    emit(loadAO, t1, t2, result)
  case NUM:
    result ← NextRegister()
    emit(loadI, val(node), result)
  return result
```

Para `a - b × c`:
```iloc
loadI @a  ⇒ r1; loadAO rarp,r1 ⇒ r2
loadI @b  ⇒ r3; loadAO rarp,r3 ⇒ r4
loadI @c  ⇒ r5; loadAO rarp,r5 ⇒ r6
mult r4,r6 ⇒ r7
sub  r2,r7 ⇒ r8
```

## Alocação de Armazenamento

### Modelo Memória-a-Memória
- Todos os valores residem em memória
- Cada definição armazena resultado de volta à memória
- Análise simples, mas código ineficiente

### Modelo Registrador-a-Registrador
- Escalares que podem ser provados livre de aliasing → residem em registradores virtuais
- IR gerada com registradores virtuais ilimitados
- Back end faz register allocation real

## Redução da Demanda por Registradores

**Ordem de avaliação** dos filhos afeta quantos registradores são necessários:
- **Regra de Sethi-Ullman**: avaliar primeiro o subárvore que requer mais registradores
- Reordenação válida para operadores comutativos e associativos

## Layout de Dados

```
┌──────────────────┐  endereço baixo
│    código        │
├──────────────────┤
│    estático      │  variáveis static/globais
├──────────────────┤
│    heap          │  cresce para cima ↑
│         ↑        │
│         ↓        │
│    pilha         │  cresce para baixo ↓
└──────────────────┘  endereço alto
```

## Alocação de Variáveis

- **Estáticas/globais**: endereço fixo em compile time, na área Static
- **Locais**: offset no AR (pilha), acessadas via ARP + offset
- **Temporários**: registradores ou área de temporários no AR

## Expressões Aritméticas

Tipos de operadores e estratégias:
- **+, -, ×, ÷**: treewalk direto; comutatividade permite reordenar
- **Exponenciação, trig**: inline expansion ou chamada de biblioteca
- **Divisão inteira por potência de 2**: substituída por shift right

## Expressões de Tipo Misto

Quando operandos têm tipos diferentes, o compilador insere conversões:
```
integer + real → converter integer para real → real + real
```
Regras definidas pelo sistema de tipos da linguagem (ex.: FORTRAN 77 tem tabela de coerções implícitas).

## Assignment como Operador

Em C, assignment retorna o valor atribuído:
```c
a = b = c = 0;   // chain assignment
```
O compilador gera o valor calculado e o armazena + mantém o valor no registrador para uso encadeado.

## Arrays

Acesso a `A[i,j]` (row-major, base 1, 10×10 elementos de 4 bytes):
```iloc
subI ri, 1   ⇒ r1
multI r1, 10 ⇒ r2
subI rj, 1   ⇒ r3
add r2, r3   ⇒ r4
multI r4, 4  ⇒ r5
loadI @A     ⇒ r6
add r5, r6   ⇒ r7
load r7      ⇒ rAij
```

- **Row-major**: linha varia mais lentamente (C, Pascal)
- **Column-major**: coluna varia mais lentamente (FORTRAN)

## Strings e Estruturas

- Strings: operações via loop (null-terminated C) ou tamanho explícito (Pascal)
- Structs: campo acessado via ponteiro base + offset fixo em compile time
- Unions: campo interpretação depende de tag em runtime

## Chamadas de Função em Expressões

Chamada no meio de expressão: salvar estado (registradores caller-save), emitir call, restaurar estado. Resultado retornado em registrador acordado.

## Relações

- [[IR Linear — Código de Três Endereços]] — IR sobre a qual o código shape opera
- [[IR Graphical — Árvores e Grafos]] — treewalk opera sobre a AST
- [[Abstração de Procedimento]] — acesso a variáveis via AR; chamadas dentro de expressões
- [[Forma de Código — Booleanos e Controle]] — expressões booleanas e fluxo de controle
- [[Back End do Compilador]] — recebe IR com code shape definido e finaliza seleção de instrução
