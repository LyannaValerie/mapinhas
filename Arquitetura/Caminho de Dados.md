---
title: Caminho de Dados (Datapath)
tags:
  - arquitetura
  - microarquitetura
  - processador
aliases:
  - Datapath
  - Caminho de Dados
---

# Caminho de Dados (Datapath)

Conjunto de registradores, barramentos e [[ULA]] que movem e transformam dados durante a execução de microinstruções.

## Relações SPO

| Sujeito | Predicado | Objeto |
|---|---|---|
| Datapath | composto por | registradores + barramentos + ULA |
| Barramento B | alimenta | entrada da ULA (via latch B) |
| Barramento A | alimenta | entrada H da ULA |
| ULA | escreve resultado em | barramento C |
| Barramento C | distribui para | registradores selecionados |
| Microinstrução | controla | seleção de fonte/destino do datapath |

## Registradores do Datapath (Mic-1)

| Registrador | Função |
|---|---|
| **PC** | Program Counter — endereço da próxima instrução |
| **MBR** | Memory Buffer Register — byte lido da memória (8 bits, leitura de instrução) |
| **MBR2** | Versão de 16 bits do MBR (extensão de sinal ou zero) |
| **MAR** | Memory Address Register — endereço para acesso a dados (32 bits) |
| **MDR** | Memory Data Register — palavra lida/escrita (32 bits) |
| **SP** | Stack Pointer — topo da pilha |
| **LV** | Local Variable pointer — base do frame corrente |
| **CPP** | Constant Pool Pointer |
| **TOS** | Top Of Stack — cache do valor no topo (otimização) |
| **OPC** | Opcode register — registrador temporário |
| **H** | Holding register — entrada A da ULA; único registrador que pode alimentar barramento A |

> [!note] H é especial
> Apenas H pode ser lido pelo barramento A. Os demais registradores alimentam somente o barramento B. A ULA sempre recebe H no lado A e o resultado do barramento B no lado B.

## Barramentos e Temporização

O datapath opera em **subciclos** dentro de um ciclo de clock:

```
Δw  Δx        Δy            Δz
|---|---------|-------------|---|
    Latch B   ULA calcula   Escreve C → reg
```

| Subciclo | Evento |
|---|---|
| **Δw** | valores propagam do registrador para barramento B |
| **Δx** | latch B captura valor; ULA começa a operar |
| **Δy** | resultado da ULA estabiliza no barramento C |
| **Δz** | barramento C escreve nos registradores destino |

- **Barramento B** (leitura): multiplexado — microinstrução seleciona qual registrador lê.
- **Barramento C** (escrita): largura total; bits individuais habilitam escrita em cada registrador.
- **Barramento A**: apenas H; sem multiplexador.

## Operações da ULA no Datapath

A ULA recebe H (lado A) e valor do barramento B (lado B). Funções selecionadas pelo campo `ULA` da microinstrução:

| Código | Operação |
|---|---|
| 000 | A (passa H) |
| 001 | B (passa barramento B) |
| 010 | NOT B |
| 011 | NOT A |
| 100 | A + B |
| 101 | A + B + 1 |
| 110 | A + 1 |
| 111 | A − B (complemento) |

Saídas adicionais: flags **N** (negativo) e **Z** (zero) → usados por JAM para desvio condicional.

## Leitura e Escrita de Memória no Mesmo Ciclo

Limitação: escrever em MAR e ler memória ativada pelo mesmo MAR são operações que podem coexistir se:
- escrita em MAR ocorre no final do ciclo (Δz)
- leitura de memória usa o MAR **do ciclo anterior**

Resultado: pipelineamento implícito — endereço calculado num ciclo, dado disponível no próximo.

## Formato da Microinstrução (Mic-1, 36 bits)

```
[ Addr(9) | J(3) | ULA(8) | C(9) | Mem(3) | B(4) ]
```

| Campo | Bits | Função |
|---|---|---|
| **Addr** | 9 | Endereço da próxima microinstrução no controle store |
| **J (JAM)** | 3 | JMPC, JAMN, JAMZ — controle de desvio |
| **ULA** | 8 | SLL8, SRA1, F0, F1, EN_A, EN_B, INV_A, INC |
| **C** | 9 | Máscara de escrita nos registradores (H, OPC, TOS, CPP, LV, SP, PC, MDR, MAR) |
| **Mem** | 3 | READ, WRITE, FETCH |
| **B** | 4 | Seleção do registrador fonte para barramento B (0–8) |

### Campo JAM

- **JAMZ**: se Z=1, OR com MPC[8] (inverte bit alto do próximo endereço)
- **JAMN**: se N=1, OR com MPC[8]
- **JMPC**: MPC[7:0] ← MBR (desvio indexado pelo opcode — base do despachante de instruções)

## Controle Store e MPC/MIR

- **Controle Store**: memória ROM de 512 × 36 bits contendo todas as microinstruções.
- **MPC** (Micro Program Counter): endereço da próxima microinstrução.
- **MIR** (Micro Instruction Register): registrador que mantém a microinstrução corrente enquanto executa.

Ciclo básico:
```
MIR ← ControleStore[MPC]
executa MIR
MPC ← próximo endereço (Addr ou JAM)
```

## Ver também

- [[Microprogramação]] — Mic-1 a Mic-4, evolução da microarquitetura
- [[ULA]] — unidade que processa os dados
- [[Registrador]] — elementos de armazenamento do datapath
- [[Pipeline]] — Mic-3/Mic-4 introduzem latches entre estágios do datapath
- [[Fundamentos/Pilha]] — SP e LV são registradores-chave do datapath
