---
title: Aritmética de Multiprecisão
aliases:
  - multiprecision arithmetic
  - extended-precision arithmetic
  - aritmética de precisão estendida
  - aritmética multipalavra
  - ADC x86
  - SBB x86
tags:
  - computação/arquitetura
  - computação/assembly
date: 2026-04-24
source: "Advanced Arithmetic, cap. 8"
created_by: "criado pelo Codex"
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Aritmética de Multiprecisão

Aritmética de multiprecisão estende operações inteiras para valores maiores que o tamanho nativo da [[ISA]]. Em vez de uma única instrução operar todo o inteiro, o programa divide o valor em palavras menores e propaga carry ou borrow entre elas.

## Relações (SPO)

- Inteiro de multiprecisão → composto por → palavras de máquina
- Palavra menos significativa → processada antes de → palavra mais significativa
- `adc` → soma → operando + destino + carry flag
- `sbb` → subtrai → operando + borrow codificado na carry flag
- Carry flag → encadeia → operações entre palavras
- Multiprecisão → complementa → [[Aritmética Inteira]]

---

## Modelo

Um inteiro de 96 bits em x86 de 32 bits pode ser representado como três double words:

```text
alto    meio    baixo
bits95  bits63  bits31
```

Operações começam pelo bloco menos significativo. O resultado local define flags que alimentam a próxima palavra.

---

## Adição

Fluxo típico:

1. Somar as palavras menos significativas com `add`.
2. Somar palavras seguintes com `adc`.
3. Repetir até a palavra mais significativa.

```asm
add eax, [b_low]
adc edx, [b_mid]
adc ecx, [b_high]
```

`add` inicial produz carry quando há overflow unsigned no bloco baixo. `adc` incorpora esse carry na soma seguinte.

> [!note]
> A técnica é igual à soma decimal manual: soma dígitos baixos, propaga vai-um, continua para dígitos altos. A diferença é que cada "dígito" é uma palavra de 8, 16 ou 32 bits.

---

## Subtração

Subtração usa o mesmo encadeamento, mas com borrow:

```asm
sub eax, [b_low]
sbb edx, [b_mid]
sbb ecx, [b_high]
```

Em x86, borrow é refletido pela carry flag. `sbb` subtrai o operando e também subtrai 1 quando a operação anterior exigiu empréstimo.

---

## Operandos de Tamanhos Diferentes

Antes de operar valores com larguras diferentes, ambos precisam ser convertidos para formato compatível:

- unsigned menor → zero extension
- signed menor → sign extension
- destino maior → deve reservar palavras suficientes para carry final

Misturar signed e unsigned sem extensão explícita muda a interpretação dos bits e pode produzir resultado semanticamente errado.

---

## Ver também

- [[Aritmética Inteira]] — overflow, complemento de dois e flags conceituais
- [[Registradores x86]] — flags CF, OF, SF e ZF
- [[Linguagem Assembly]] — instruções e sintaxe de baixo nível
- [[Aritmética MIPS — Inteiros]] — multiplicação/divisão inteiras no ponto de vista MIPS
- [[Sistemas de Representação]] — extensão de sinal e zero extension

