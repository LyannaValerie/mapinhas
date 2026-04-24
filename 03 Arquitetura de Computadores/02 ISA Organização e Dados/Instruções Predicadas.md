---
title: Instruções Predicadas
aliases:
  - predicated instructions
  - predicated execution
  - if-conversion
  - full predication
  - conditional moves
  - guard predicate
tags:
  - computação/arquitetura
  - computação/ILP
date: 2026-04-22
source: "P&H Apêndice H §H.4"
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Instruções Predicadas

Mecanismo onde a execução de uma instrução é **condicionada** a um registrador predicado (booleano). Se o predicado for falso, a instrução é um no-op e **não gera exceção**.

## Relações (SPO)

| Sujeito | Predicado | Objeto |
|---|---|---|
| instrução predicada | torna-se no-op quando | predicado = falso |
| instrução predicada | não gera exceção quando | predicado = falso |
| if-conversion | transforma | if-then-else em instruções predicadas |
| if-conversion | elimina | branches (desvios) |
| full predication | aplica predicado a | todas as instruções da ISA |
| conditional move (cmov) | é | forma limitada de predicação |
| [[IA-64 e EPIC]] | tem | 64 registradores predicado de 1 bit |

## Motivação

Problema do [[Escalonamento Global de Código]]: mover instrução acima de branch pode:
- Causar exceção indevida (load com endereço inválido antes do branch que o protegia)
- Exigir compensation code custoso

Solução: converter o branch em predicado e manter a instrução no lugar — ela só "age" se o predicado for verdadeiro.

## If-Conversion

Converte if-then-else em código predicado:

```c
// Código original
if (R10 != 0)
    R8 = MEM[R10];   // load — pode dar fault se R10 == 0

// Com instrução predicada:
p1 = (R10 != 0)       // seta predicado
(p1) R8 = MEM[R10]    // load só executa se p1 = true
                       // se p1 = false → no-op, sem fault
```

Após if-conversion: **sem branch → sem misprediction penalty**.

## Conditional Move vs. Full Predication

| Aspecto | Conditional Move (cmov) | Full Predication |
|---|---|---|
| Escopo | Move de um valor | Qualquer instrução |
| Exceções com pred=false | Pode gerar (instrução executa) | Não gera (instrução é no-op) |
| Útil para | Sequências curtas | Blocos inteiros |
| ISA | Mais simples de adicionar | Requer revisão da ISA inteira |

> [!warning] Limitação do cmov
> Usando `cmov` para eliminar um branch que guarda um bloco grande de código, é necessário executar **todos** os cmovs mesmo quando a condição é falsa — overhead cresce proporcionalmente ao tamanho do bloco.

## Full Predication (IA-64)

[[IA-64 e EPIC]] tem 64 registradores predicado de 1 bit (`p0`–`p63`; `p0` = sempre true).

Instrução de comparação escreve em **par** de predicados (complementares):
```
cmp.eq p1,p2 = R3, R4   // p1=true se R3==R4, p2=true se R3!=R4
```

Qualquer instrução pode ser guardada:
```
(p1) add R5 = R6, R7    // executa só se p1=true
(p2) sub R5 = R6, R7    // executa só se p2=true
```

If-then-else completo sem branches:
```c
if (R10 != 0) { R8 = MEM[R10]; }
else          { R8 = R11 + R12; }

// IA-64:
cmp.ne p1,p2 = R10, R0
(p1) ld8   R8 = [R10]
(p2) add   R8 = R11, R12
```

## Regra de Exceção

Instrução predicada com `pred = false`:
- **Não gera exceção** (não cria fault de memória, divisão por zero, etc.)
- Se `pred = true` e ocorre exceção legal (ex: page fault) → exceção é tratada normalmente

Esta propriedade é **fundamental** para mover loads antes de branches que os protegem.

## Predicated Execution e [[Software Pipelining]]

Loops com desvios internos (if-statements) podem ser convertidos via if-conversion antes de aplicar software pipelining — remove a complexidade de controle de fluxo interno.

## Ver também

- [[IA-64 e EPIC]] — implementação com 64 predicados e predicação completa
- [[Escalonamento Global de Código]] — instruções predicadas simplificam compensation code
- [[Especulação por Compilador]] — complementa predicação para mover loads além de branches
- [[Software Pipelining]] — usa predicação para tratar loops com desvios internos
- [[Previsão de Desvio]] — predicação elimina necessidade de previsão em alguns casos
