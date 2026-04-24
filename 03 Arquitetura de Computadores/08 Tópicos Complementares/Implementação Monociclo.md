---
title: Implementação Monociclo
tags:
  - arquitetura
  - mips
  - processador
  - controle
aliases:
  - Single-Cycle Implementation
  - Ciclo Único
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Implementação Monociclo

Implementação do processador MIPS onde **toda instrução executa em exatamente 1 ciclo de clock**. O ciclo deve ser longo o suficiente para acomodar a instrução mais lenta (P&H Cap. 5.4).

## Controle da ULA

Hierarquia de dois níveis:

```
Opcode → Unidade de Controle Principal → ALUOp (2 bits)
                                              ↓
funct[5:0] + ALUOp → ALU Control → ALU Control Input (4 bits)
```

| ALUOp | Instrução | Ação ULA |
|---|---|---|
| `00` | lw, sw | add (calcula endereço) |
| `01` | beq | subtract (compara) |
| `10` | R-type | determinado pelo campo funct |

### Tabela ALU Control (R-type, ALUOp=10)

| funct | Operação | Bits ULA |
|---|---|---|
| 100000 | add | 0010 |
| 100010 | subtract | 0110 |
| 100100 | AND | 0000 |
| 100101 | OR | 0001 |
| 101010 | slt | 0111 |

## Unidade de Controle Principal

Entradas: **Op[5:0]** (opcode da instrução).
Saídas: 7 sinais de 1 bit + ALUOp (2 bits).

| Sinal | Função |
|---|---|
| **RegDst** | 0 = rt é destino (load); 1 = rd é destino (R-type) |
| **ALUSrc** | 0 = Reg[rt]; 1 = sign_extend(imm) como 2ª entrada ULA |
| **MemtoReg** | 0 = resultado ULA → reg; 1 = dado memória → reg |
| **RegWrite** | habilita escrita no register file |
| **MemRead** | habilita leitura da memória de dados |
| **MemWrite** | habilita escrita na memória de dados |
| **Branch** | habilita lógica de desvio (AND com Zero da ULA) |

### Tabela de Verdade (por instrução)

| Instrução | RegDst | ALUSrc | MemtoReg | RegWrite | MemRead | MemWrite | Branch | ALUOp |
|---|---|---|---|---|---|---|---|---|
| R-type | 1 | 0 | 0 | 1 | 0 | 0 | 0 | 10 |
| lw | 0 | 1 | 1 | 1 | 1 | 0 | 0 | 00 |
| sw | X | 1 | X | 0 | 0 | 1 | 0 | 00 |
| beq | X | 0 | X | 0 | 0 | 0 | 1 | 01 |

## Campos da Instrução no Datapath

| Campo | Bits | Uso |
|---|---|---|
| op | [31:26] | entrada da unidade de controle |
| rs | [25:21] | Read register 1 |
| rt | [20:16] | Read register 2; Write register (load) |
| rd | [15:11] | Write register (R-type) |
| imm/offset | [15:0] | sign-extend → ULA ou branch target |

## Caminho Crítico e Limitação

O clock deve acomodar a instrução mais lenta: **load word** (5 etapas sequenciais):

```
Instruction Memory → Register File Read → ALU → Data Memory → Register File Write
```

Tempo típico (exemplo):
- lw: 600ps (I-mem) + 200ps (reg read) + 200ps (ALU) + 350ps (D-mem) + 200ps (reg write) = **~1000–1400 ps**

> [!warning] Ineficiência do monociclo
> Instruções rápidas (R-type, beq) ficam ociosas esperando o ciclo longo exigido por lw. Solução: [[Implementação Multiciclo]] ou [[Pipeline]].

## Relações SPO

| Sujeito | Predicado | Objeto |
|---|---|---|
| Unidade de controle | decodifica | opcode → sinais de controle |
| ALU Control | combina | ALUOp + funct → operação ULA |
| Branch | requer | Zero AND Branch → PCSrc |
| Ciclo único | limitado por | instrução mais lenta (lw) |
| RegDst MUX | seleciona | rd (R-type) ou rt (load) |

## Ver também

- [[Caminho de Dados — MIPS]] — datapath usado por esta implementação
- [[Implementação Multiciclo]] — alternativa com ciclos variáveis
- [[Pipeline]] — técnica para aumentar throughput
- [[ULA]] — detalhes da unidade aritmético-lógica
- [[MIPS — Formato de Instrução]] — campos da instrução
