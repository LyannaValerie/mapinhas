---
title: Organização de Chips de Memória
aliases:
  - chip select
  - CS
  - OE
  - RD
  - decodificador de endereços
  - endereçamento de memória
  - banco de memória
  - organização de memória
tags:
  - computação/arquitetura
  - computação/hardware
date: 2026-04-18
source: "Organização Estruturada de Computadores — Tanenbaum, Cap. 3"
---

# Organização de Chips de Memória

Como chips de memória individuais expõem pinos de controle e como múltiplos chips são combinados para construir sistemas de memória maiores.

## Relações (SPO)

- Chip de memória → expõe → pinos de endereço (A), dados (D), CS, RD/WR, OE
- CS (Chip Select) → habilita → chip específico na leitura/escrita
- Decodificador de endereços → usa → bits de alto ordem do barramento de endereços
- Chips em paralelo → expandem → largura de palavra (bits por acesso)
- Bancos em série → expandem → capacidade total (número de endereços)
- Tri-state → permite → múltiplos chips compartilhar o mesmo barramento de dados

---

## Pinos de um Chip de Memória

| Pino | Nome | Direção | Função |
|---|---|---|---|
| **A0–An** | Address | Entrada | Seleciona qual célula interna acessar |
| **D0–Dm** | Data | Bidirecional (ou separado in/out) | Dado lido ou escrito |
| **CS** | Chip Select | Entrada | Habilita o chip; quando desativado, saídas em tri-state |
| **RD** | Read | Entrada | Seleciona leitura (vs. escrita) |
| **WR** | Write | Entrada | Seleciona escrita |
| **OE** | Output Enable | Entrada | Habilita a saída de dados (tri-state quando desativado) |

Um chip com n pinos de endereço endereça **2ⁿ** células. Um chip com m pinos de dados armazena **m bits por endereço**.

> [!example] Chip 512K × 8 bits
> 19 pinos de endereço (2¹⁹ = 524.288 endereços), 8 pinos de dados.
> Capacidade total: 512 KB.

---

## Combinando Chips

### Expandindo a Largura de Palavra

Para acessar palavras de 16 bits com chips de 8 bits: colocar **2 chips em paralelo**, conectados ao mesmo barramento de endereços e ao mesmo sinal CS. Cada chip fornece 8 dos 16 bits de dados.

```
Barramento de endereços (A0–A18) ─────┬───────────────────────┐
                                       │                       │
                                  [Chip 0]                [Chip 1]
                                  D0–D7                   D8–D15
                                       │                       │
                          ─────────────┴───────────────────────┴──── Barramento de dados (D0–D15)
```

### Expandindo a Capacidade (Bancos)

Para dobrar a capacidade: usar 2 conjuntos de chips, cada um respondendo a um intervalo diferente de endereços. O bit de endereço de **alto ordem** (ex: A19) seleciona qual conjunto está ativo.

```
A19 = 0 → CS ativo no Banco 0 (endereços 0x00000–0x7FFFF)
A19 = 1 → CS ativo no Banco 1 (endereços 0x80000–0xFFFFF)
```

---

## Decodificador de Endereços

Circuito combinatório que usa os **bits de alto ordem** do barramento de endereços para gerar os sinais **CS** de cada banco.

```
A19 ──┬──► NOT ──► CS do Banco 0  (ativo quando A19 = 0)
      └────────► CS do Banco 1  (ativo quando A19 = 1)
```

Para 4 bancos, usa-se um **decodificador 2:4**: bits A19 e A18 selecionam qual dos 4 sinais CS é ativado. Ver [[Circuitos Combinatórios#Decodificador (Decoder)]].

> [!note] Por que bits de alto ordem?
> Os bits de alto ordem determinam o **banco** (região do espaço de endereços). Os bits de baixo ordem selecionam a **célula dentro do banco**. Essa divisão é a base do endereçamento de memória hierárquico.

---

## Tri-State e Barramento de Dados Compartilhado

Apenas o chip com CS ativo deve colocar dados no barramento. Os outros chips devem estar em modo **tri-state** (alta impedância) para não interferir.

O pino **OE** (Output Enable) controla isso: quando OE = 0, as saídas de dados ficam em tri-state independentemente de CS. Isso permite desacoplar a seleção do chip do controle de direção do barramento.

Ver [[Portas Lógicas#Porta Tri-State]].

---

## Exemplo Completo: Memória de 2 MB com chips 512K × 8

Objetivo: 2 MB com largura de 16 bits.

- Largura: 2 chips de 8 bits em paralelo → 16 bits por acesso
- Capacidade: 4 conjuntos de 2 chips → 4 × 512 KB = 2 MB
- Pinos de endereço: A0–A18 (19 bits → 512K endereços por banco)
- Decodificador: A19, A20 → sinal CS para cada um dos 4 bancos

Total: **8 chips** (4 bancos × 2 chips por banco).

---

## Ver também

- [[RAM — Tecnologias e Módulos]] — SRAM, DRAM, DDR, módulos DIMM
- [[Barramento]] — barramento de endereços, dados e controle; sincronia e arbitragem
- [[Circuitos Combinatórios]] — decodificadores 2:4, 3:8 usados no address decoding
- [[Portas Lógicas]] — tri-state: o mecanismo que permite múltiplos chips compartilhar o barramento
- [[DMA]] — controlador DMA também usa os mesmos barramentos para acessar memória diretamente