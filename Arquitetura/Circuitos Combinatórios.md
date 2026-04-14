---
title: Circuitos Combinatórios
aliases:
  - multiplexer
  - MUX
  - decoder
  - decodificador
  - comparador
  - barrel shifter
  - shifter
  - half-adder
  - full-adder
  - somador completo
  - ripple-carry
  - carry-select
  - somador binário
tags:
  - computação/arquitetura
  - computação/fundamentos
date: 2026-04-12
source: "Organização Estruturada de Computadores — Tanenbaum, Cap. 3"
---

# Circuitos Combinatórios

Circuitos digitais cuja saída depende **somente das entradas atuais** (sem memória de estados anteriores). Construídos com [[Portas Lógicas]] e projetados com [[Álgebra Booleana]].

→ Circuitos com memória: [[Latch e Flip-Flop]]  
→ Construídos com: [[Portas Lógicas]]  
→ Compõem: [[ULA]] (somadores, shifters, lógica)

---

## Relações (SPO)

- Multiplexer → seleciona → 1 de 2ⁿ entradas via n linhas de seleção
- Decoder → ativa → 1 de 2ⁿ saídas (codificação one-hot)
- Full-adder → soma → A + B + Cin → Sum + Cout
- Ripple-carry adder → encadeia → n full-adders (carry propaga estágio a estágio)
- Carry-select adder → pré-computa → resultado para Cin=0 e Cin=1 em paralelo → mais rápido
- Barrel shifter → desloca → entrada por 0 a n-1 posições em 1 ciclo

---

## Multiplexer (MUX)

Seleciona 1 de **2ⁿ** entradas para a saída com base em **n** linhas de seleção.

### MUX 2:1

| S | Y |
|---|---|
| 0 | A |
| 1 | B |

Expressão: `Y = (NOT S · A) + (S · B)`

### MUX 4:1

4 entradas (I0–I3), 2 linhas de seleção (S1, S0):

| S1 | S0 | Y |
|---|---|---|
| 0 | 0 | I0 |
| 0 | 1 | I1 |
| 1 | 0 | I2 |
| 1 | 1 | I3 |

> [!tip] MUX como implementador universal
> Um MUX 2ⁿ:1 pode implementar **qualquer função booleana** de n variáveis: conectar as entradas de dados às saídas desejadas da tabela-verdade (0 ou 1). As variáveis são as linhas de seleção.

---

## Decoder (Decodificador)

n entradas → 2ⁿ saídas. Exatamente **uma saída ativa** (one-hot) para cada combinação de entrada.

### Decoder 2:4

| A1 | A0 | Y0 | Y1 | Y2 | Y3 |
|---|---|---|---|---|---|
| 0 | 0 | 1 | 0 | 0 | 0 |
| 0 | 1 | 0 | 1 | 0 | 0 |
| 1 | 0 | 0 | 0 | 1 | 0 |
| 1 | 1 | 0 | 0 | 0 | 1 |

**Usos:** seleção de banco de memória, geração de sinais de habilitação de chip (chip select), mapeamento de endereços.

---

## Comparador

Compara dois números de n bits: A e B. Saídas: `A=B`, `A>B`, `A<B`.

Para 1 bit:
- `A=B` quando `NOT(A XOR B)`
- `A>B` quando `A AND NOT B`
- `A<B` quando `NOT A AND B`

Para n bits: encadear 1-bit comparators, propagando resultado de bits mais significativos para menos significativos.

**Uso:** desvios condicionais na [[ULA]] (flags de comparação).

---

## Shifter / Barrel Shifter

Desloca uma palavra de n bits por qualquer número de posições em **1 ciclo de clock**.

**Barrel shifter de 8 bits:** implementado com 3 estágios de MUX em cascata (desloca por 4, 2 e 1 bit independentemente → combinação permite qualquer deslocamento 0–7).

Tipos de deslocamento:
| Tipo | Descrição | Bit inserido |
|---|---|---|
| Lógico esquerda | Multiplica por 2 | 0 no LSB |
| Lógico direita | Divide por 2 (sem sinal) | 0 no MSB |
| Aritmético direita | Divide por 2 (com sinal) | Cópia do MSB |
| Rotação | Bits saem e entram no lado oposto | Bit que saiu |

**Integração na [[ULA]]:** a maioria das ULAs modernas inclui barrel shifter interno para executar instruções de shift em 1 ciclo.

---

## Half-Adder (Somador Parcial)

Soma 2 bits: A + B → Sum + Carry.

| A | B | Sum | Carry |
|---|---|---|---|
| 0 | 0 | 0 | 0 |
| 0 | 1 | 1 | 0 |
| 1 | 0 | 1 | 0 |
| 1 | 1 | 0 | 1 |

Expressões:
```
Sum   = A XOR B
Carry = A AND B
```

Implementação: 1 porta XOR + 1 porta AND.

---

## Full-Adder (Somador Completo)

Soma 3 bits: A + B + **Cin** → Sum + Cout.  
Cin = carry de entrada (do estágio anterior na cadeia).

| A | B | Cin | Sum | Cout |
|---|---|---|---|---|
| 0 | 0 | 0 | 0 | 0 |
| 0 | 0 | 1 | 1 | 0 |
| 0 | 1 | 0 | 1 | 0 |
| 0 | 1 | 1 | 0 | 1 |
| 1 | 0 | 0 | 1 | 0 |
| 1 | 0 | 1 | 0 | 1 |
| 1 | 1 | 0 | 0 | 1 |
| 1 | 1 | 1 | 1 | 1 |

Expressões:
```
Sum  = A XOR B XOR Cin
Cout = (A AND B) OR (Cin AND (A XOR B))
```

Implementação: 2 half-adders + 1 porta OR.

---

## Ripple-Carry Adder (Somador com Carry Ondulante)

Encadeia n full-adders para somar dois números de n bits.

```
     A3 B3    A2 B2    A1 B1    A0 B0
      ↓  ↓     ↓  ↓     ↓  ↓     ↓  ↓
Cout─[FA]─C3─[FA]─C2─[FA]─C1─[FA]─ 0 (Cin)
      ↓          ↓         ↓         ↓
      S3          S2        S1        S0
```

**Vantagem:** simples, poucos transistores.  
**Desvantagem:** lento — o carry deve se **propagar** (ripple) de LSB para MSB antes que os bits mais significativos possam ser computados.

Atraso total = n × atraso de 1 full-adder.

---

## Carry-Select Adder

Resolve o gargalo do ripple-carry pré-computando dois resultados em paralelo para cada grupo de bits:

1. Resultado assumindo **Cin = 0**
2. Resultado assumindo **Cin = 1**

Quando o carry real chega (da metade menos significativa), um MUX seleciona o resultado correto.

```
Bits [7:4]     Bits [3:0]
┌──────────┐   ┌──────┐
│Cin=0 → R0│   │      │
│          │MUX│ adder│─── Cout → seleciona R0 ou R1
│Cin=1 → R1│───│      │
└──────────┘   └──────┘
```

**Resultado:** atraso aproximadamente proporcional a √n em vez de n.

---

## Ver também

- [[Portas Lógicas]] — AND, OR, XOR constroem todos os circuitos acima
- [[Álgebra Booleana]] — síntese e simplificação dos circuitos
- [[ULA]] — ALU construída a partir de somadores, shifters e lógica combinatória
- [[Latch e Flip-Flop]] — acrescenta memória aos circuitos combinatórios
