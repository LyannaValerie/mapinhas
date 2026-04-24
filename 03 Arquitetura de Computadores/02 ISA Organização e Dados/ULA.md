---
title: ULA
aliases:
  - ALU
  - Arithmetic Logic Unit
  - Unidade Lógica e Aritmética
  - Central de Aritmética
  - CA
  - unidade de inteiros
  - unidade de ponto flutuante
tags:
  - computação/fundamentos
  - computação/arquitetura
date: 2026-04-06
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# ULA — Unidade Lógica e Aritmética

## Definição
Componente responsável pela execução de cálculos aritméticos e lógicos. Na nomenclatura original do [[Modelo de Von Neumann]], é chamada de **Central de Aritmética (CA)**. Em computadores modernos, está integrada dentro do [[Processador]].

## Relações (SPO)
- ULA → executa → cálculos aritméticos e lógicos
- ULA → integrada em → [[Processador]] (nível 1 — microarquitetura)
- ULA → conectada a → [[Registrador|registradores]] formando o **caminho de dados**
- ULA → corresponde a → Central de Aritmética (CA) no [[Modelo de Von Neumann]]
- ULA → construída de → [[Circuitos Combinatórios]] (somadores, shifter, lógica)
- ULA de n bits → formada por → n fatias de 1 bit encadeadas (ripple-carry)

## Nomenclaturas

| Nome | Contexto |
|---|---|
| Central de Aritmética (CA) | Nomenclatura original de Von Neumann |
| ULA | Termo moderno geral |
| ALU *(Arithmetic Logic Unit)* | Equivalente em inglês |
| Unidade de inteiros | Quando opera somente com números inteiros |
| Unidade de ponto flutuante | Quando opera com números reais |

## Estrutura do Circuito da ULA

Uma ULA de n bits é construída encadeando n **fatias de 1 bit** (1-bit ALU slices).

### Entradas e Saídas de 1 Bit

| Sinal | Direção | Descrição |
|---|---|---|
| A, B | Entrada | Operandos de 1 bit |
| F0, F1 | Entrada | Código de função (seleciona operação) |
| Cin | Entrada | Carry de entrada (da fatia anterior) |
| Result | Saída | Resultado de 1 bit |
| Cout | Saída | Carry de saída (para a próxima fatia) |

### Operações por Código de Função

| F1 | F0 | Operação |
|---|---|---|
| 0 | 0 | A AND B |
| 0 | 1 | A OR B |
| 1 | 0 | NOT B |
| 1 | 1 | A + B (adição; usa Cin/Cout) |

### ULA de n bits

```
A[0]B[0] F0F1       A[1]B[1] F0F1       A[n-1]B[n-1] F0F1
   ↓       ↓            ↓       ↓              ↓         ↓
 [1-bit ALU] ─Cout→ [1-bit ALU] ─Cout→ … ─Cout→ [1-bit ALU]
    ↓              0→Cin               Cout→ Overflow/Carry
  R[0]            R[1]                         R[n-1]
```

- Cin do bit 0 = 0 para adição normal; = 1 para subtração (complemento de dois: NOT B + 1)
- **Overflow**: detectado quando Cout do bit n-1 ≠ Cout do bit n-2 (carry entrando e saindo do bit de sinal diferentes)

### Flags de Status

| Flag | Condição |
|---|---|
| **Zero** | Todos os bits do resultado = 0 |
| **Carry** | Cout do bit mais significativo |
| **Overflow** | Carry-in ≠ Carry-out no bit de sinal |
| **Negativo** | Bit mais significativo do resultado = 1 |

### Barrel Shifter Integrado

A maioria das ULAs inclui um [[Circuitos Combinatórios#Shifter / Barrel Shifter|barrel shifter]] interno. Na borda de operação de shift, o shifter desloca a entrada pelo número de posições especificado em 1 ciclo, ignorando o caminho somador.

## Ver também
- [[Processador]]
- [[Registrador]]
- [[Modelo de Von Neumann]]
- [[Níveis de Abstração]]
- [[Hardware]]
- [[Circuitos Combinatórios]] — somadores e shifters que compõem a ULA
- [[Portas Lógicas]] — AND/OR/NOT no nível mais básico
