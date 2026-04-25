---
title: Aritmética Decimal BCD
aliases:
  - BCD
  - binary-coded decimal
  - decimal empacotado
  - packed decimal
  - aritmética decimal x86
  - fbld
  - fbstp
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

# Aritmética Decimal BCD

BCD representa cada dígito decimal com 4 bits. O valor binário de cada nibble corresponde ao dígito decimal de 0 a 9; combinações de 10 a 15 são inválidas para dígitos BCD.

## Relações (SPO)

- BCD → preserva → estrutura decimal explícita
- Dígito decimal → ocupa → 4 bits
- Dois dígitos BCD → cabem em → 1 byte
- BCD → desperdiça → combinações binárias por nibble
- Aritmética decimal → usada quando → semântica base 10 é obrigatória
- x87 FPU → carrega/armazena → decimal empacotado via `fbld` e `fbstp`

---

## Representação

| Decimal | BCD |
|---|---|
| 0 | `0000` |
| 1 | `0001` |
| 2 | `0010` |
| 3 | `0011` |
| 4 | `0100` |
| 5 | `0101` |
| 6 | `0110` |
| 7 | `0111` |
| 8 | `1000` |
| 9 | `1001` |

Exemplo: `42` em BCD empacotado usa um byte:

```text
0100 0010
  4    2
```

---

## Custo

BCD é menos denso que binário puro. Um byte binário representa 0-255; um byte BCD empacotado representa apenas 00-99.

Também há custo operacional: processadores binários executam aritmética inteira natural com mais eficiência que aritmética decimal. BCD só compensa quando conversão decimal precisa ser evitada ou quando regras de domínio exigem representação decimal exata.

---

## Decimal Empacotado na FPU

Na x87, `fbld` carrega decimal empacotado para a [[FPU]] e converte para formato interno de ponto flutuante. `fbstp` faz o caminho inverso: armazena decimal empacotado a partir do topo da pilha da FPU.

```asm
fbld  packed_decimal
fbstp packed_decimal_out
```

Essa rota permite usar a FPU como processador decimal para alguns fluxos, mas a conversão entre decimal empacotado e formato interno não é barata.

---

## Quando Usar

- entrada/saída decimal precisa preservar dígitos
- domínio exige base 10 explícita
- erro de conversão binário-decimal é inaceitável
- custo de conversão é menor que custo de manipulação binária + formatação

Para computação geral, [[Aritmética Inteira]] ou [[Ponto Flutuante — Operações]] costuma ser melhor escolha.

---

## Ver também

- [[Sistemas de Representação]] — bases numéricas e ponto flutuante
- [[Ponto Flutuante — Operações]] — operações em IEEE 754
- [[FPU x87 — Programação Assembly]] — pilha x87 e instruções FP
- [[Registradores x86]] — flags e registradores usados por instruções de baixo nível
- [[Aritmética de Multiprecisão]] — extensão de operações inteiras por palavras

