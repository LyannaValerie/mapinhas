---
title: Portas Lógicas
aliases:
  - AND
  - OR
  - NOT
  - NAND
  - NOR
  - XOR
  - porta lógica
  - gate
  - TTL
  - CMOS
  - tri-state
  - fan-in
  - fan-out
tags:
  - computação/arquitetura
  - computação/fundamentos
date: 2026-04-12
source: "Organização Estruturada de Computadores — Tanenbaum, Cap. 3"
---

# Portas Lógicas

Circuitos elementares que implementam operações da [[Álgebra Booleana]]. São os blocos construtores de todo hardware digital.

→ Álgebra que governa as portas: [[Álgebra Booleana]]  
→ Circuitos construídos com portas: [[Circuitos Combinatórios]], [[Latch e Flip-Flop]]  
→ Implementação física dos transistores: [[Fabricação de Chips]]

---

## Relações (SPO)

- Porta lógica → implementa → operação booleana (AND, OR, NOT…)
- NAND → é → porta universal (qualquer circuito construível somente com NANDs)
- NOR → é → porta universal (qualquer circuito construível somente com NORs)
- TTL → usa → transistores bipolares (5 V)
- CMOS → usa → par PMOS + NMOS (1,2–3,3 V modernos)
- Tri-state → adiciona → terceiro estado de alta impedância (Z)

---

## Tabelas-Verdade das Portas Principais

### NOT (inversor)

| A | NOT A |
|---|---|
| 0 | 1 |
| 1 | 0 |

### AND e OR

| A | B | A AND B | A OR B |
|---|---|---|---|
| 0 | 0 | 0 | 0 |
| 0 | 1 | 0 | 1 |
| 1 | 0 | 0 | 1 |
| 1 | 1 | 1 | 1 |

### NAND e NOR

NAND = NOT(A AND B). NOR = NOT(A OR B).

| A | B | NAND | NOR |
|---|---|---|---|
| 0 | 0 | 1 | 1 |
| 0 | 1 | 1 | 0 |
| 1 | 0 | 1 | 0 |
| 1 | 1 | 0 | 0 |

### XOR e XNOR

XOR (OU exclusivo): A ⊕ B = 1 somente se A ≠ B.  
XNOR = NOT(XOR): 1 somente se A = B.

| A | B | XOR | XNOR |
|---|---|---|---|
| 0 | 0 | 0 | 1 |
| 0 | 1 | 1 | 0 |
| 1 | 0 | 1 | 0 |
| 1 | 1 | 0 | 1 |

> [!tip] Usos típicos do XOR
> - Detecção de paridade (bit de paridade)
> - Soma binária sem carry (ver [[Circuitos Combinatórios#Half-Adder (Somador Parcial)]])
> - Troca de bits (swap sem variável temporária)

---

## Universalidade de NAND e NOR

Qualquer função booleana pode ser implementada **somente com portas NAND** ou **somente com portas NOR**.

### NAND como NOT, AND, OR

```
NOT A   = NAND(A, A)
A AND B = NAND(NAND(A,B), NAND(A,B))
A OR B  = NAND(NAND(A,A), NAND(B,B))
```

### NOR como NOT, OR, AND

```
NOT A   = NOR(A, A)
A OR B  = NOR(NOR(A,B), NOR(A,B))
A AND B = NOR(NOR(A,A), NOR(B,B))
```

> [!note] Por que isso importa?
> Na fabricação de CIs, é mais barato usar um único tipo de porta. Projetos com apenas NANDs ou apenas NORs reduzem a variedade de células na biblioteca de síntese.

---

## Tecnologias de Implementação

### TTL (Transistor-Transistor Logic)

- Transistores bipolares
- Tensão de alimentação: **5 V**
- Nível lógico 0: 0–0,8 V; nível lógico 1: 2–5 V
- Margem de ruído: 0,8 V (zona proibida entre 0,8 V e 2 V)
- Família: **74xx** (ex: 7400 = 4× NAND 2 entradas)
- Consumo estático maior que CMOS; mais rápido em lógica combinatória simples (legado)

### CMOS (Complementary Metal Oxide Semiconductor)

- Par de transistores MOSFET complementares: **PMOS** (pull-up) + **NMOS** (pull-down)
- Tensão de alimentação: variável — originalmente 5 V; hoje **1,2–3,3 V** (escala com processo)
- **Consumo quase zero** em estado estático (nenhum caminho DC entre VDD e GND)
- Consumo dinâmico proporcional a: frequência × capacitância × V²
- Família: **4000 series** (CMOS pura); famílias 74HCxx, 74ACxx (CMOS rápido compatível TTL)

> [!note] Por que CMOS domina hoje
> Consumo estático praticamente nulo + escala de tensão com processo = menor consumo por transistor a cada geração. TTL tem consumo estático constante, desvantajoso em chips com bilhões de transistores.

### Nível de tensão por família (tabela comparativa)

| Família | VDD | V_L máx | V_H mín | Margem |
|---|---|---|---|---|
| TTL (74xx) | 5 V | 0,8 V | 2,0 V | 0,8 V |
| CMOS 5 V (4000) | 5 V | 1,5 V | 3,5 V | 1,5 V |
| CMOS 3,3 V | 3,3 V | 0,8 V | 2,0 V | 0,8 V |
| CMOS 1,8 V | 1,8 V | 0,6 V | 1,2 V | 0,6 V |

---

## Fan-in e Fan-out

**Fan-in**: número máximo de entradas de uma porta. Portas AND/OR padrão: 2 a 8 entradas. Mais entradas = porta mais lenta.

**Fan-out**: número máximo de entradas que a saída de uma porta consegue acionar (drive) sem degradar o sinal.
- TTL padrão: fan-out de 10
- CMOS: fan-out teórico maior (entrada capacitiva), mas limitado por atraso de propagação

> Exceder o fan-out degrada a margem de ruído e pode causar leitura incorreta nas portas conectadas.

---

## Porta Tri-State

Adiciona terceiro estado **alta impedância (Z)** além de 0 e 1. Controlada por pino **Enable (OE — Output Enable)**.

| OE | Saída |
|---|---|
| 1 | Funciona normalmente (0 ou 1) |
| 0 | Alta impedância — desconectada do barramento |

**Uso:** conectar múltiplas saídas ao mesmo fio (barramento), garantindo que apenas uma porta esteja ativa por vez. Cada dispositivo em um barramento de dados usa tri-state para ceder o barramento quando não está transmitindo. Ver [[Barramento]].

---

## Ver também

- [[Álgebra Booleana]] — leis que governam o comportamento das portas
- [[Circuitos Combinatórios]] — circuitos construídos com portas lógicas
- [[Latch e Flip-Flop]] — circuitos sequenciais (com memória) construídos com portas
- [[Dados Binários]] — representação física de 0 e 1 em tensão elétrica
- [[Fabricação de Chips]] — transistores MOSFET subjacentes
