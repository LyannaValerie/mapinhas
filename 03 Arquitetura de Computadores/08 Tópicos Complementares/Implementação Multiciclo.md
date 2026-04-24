---
title: Implementação Multiciclo
tags:
  - arquitetura
  - mips
  - processador
  - controle
  - fsm
aliases:
  - Multicycle Implementation
  - Multiciclo
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Implementação Multiciclo

Processador MIPS onde cada instrução executa em **múltiplos ciclos de clock**, um ciclo por etapa funcional. Permite compartilhamento de unidades e ciclos variáveis por instrução (P&H Cap. 5.5).

## Diferenças em Relação ao Monociclo

| Aspecto | Monociclo | Multiciclo |
|---|---|---|
| Memórias | 2 separadas (inst + dados) | 1 compartilhada |
| ULAs | 3 (ULA + 2 somadores) | 1 compartilhada |
| Ciclo por instrução | fixo (mais lento) | variável (3–5 ciclos) |
| Registradores temporários | nenhum | IR, MDR, A, B, ALUOut |

## Registradores Temporários

Armazenam dados entre ciclos da mesma instrução:

| Registrador | Função |
|---|---|
| **IR** (Instruction Register) | guarda instrução buscada; precisa de write control |
| **MDR** (Memory Data Register) | dado lido da memória |
| **A** | Reg[rs] lido no ciclo anterior |
| **B** | Reg[rt] lido no ciclo anterior |
| **ALUOut** | resultado da ULA do ciclo anterior |

> [!note] IR vs outros registradores temporários
> IR precisa de sinal de escrita explícito (IRWrite) pois deve manter a instrução por vários ciclos. A, B, MDR e ALUOut são sobrescritos todo ciclo — não precisam de controle de escrita.

## Sinais de Controle Adicionais

| Sinal | Função |
|---|---|
| **IorD** | seleciona PC (0) ou ALUOut (1) como endereço de memória |
| **IRWrite** | habilita escrita no IR |
| **ALUSrcA** | 0 = PC; 1 = registrador A |
| **ALUSrcB** | 00 = B; 01 = 4; 10 = sign_ext(imm); 11 = sign_ext(imm)<<2 |
| **PCWrite** | escrita incondicional do PC |
| **PCWriteCond** | escrita do PC se Zero da ULA = 1 (branch) |
| **PCSource** | 00 = ULA; 01 = ALUOut; 10 = jump addr; 11 = exceção |

## Ciclos de Execução (5 Etapas)

### Etapa 1 — Instruction Fetch (todas as instruções)
```
IR ← Memory[PC]
PC ← PC + 4
```
Sinais: MemRead, IRWrite, IorD=0, ALUSrcA=0, ALUSrcB=01, ALUOp=00, PCWrite, PCSource=00.

### Etapa 2 — Instruction Decode / Register Fetch (todas)
```
A ← Reg[IR[25:21]]
B ← Reg[IR[20:16]]
ALUOut ← PC + (sign_extend(IR[15:0]) << 2)   // branch target especulativo
```
Sinais: ALUSrcA=0, ALUSrcB=11, ALUOp=00.

### Etapa 3 — Execution / Address Computation / Branch Completion

| Instrução | Operação | Sinais-chave |
|---|---|---|
| lw/sw | `ALUOut ← A + sign_ext(IR[15:0])` | ALUSrcA=1, ALUSrcB=10, ALUOp=00 |
| R-type | `ALUOut ← A op B` | ALUSrcA=1, ALUSrcB=00, ALUOp=10 |
| beq | `if (A==B) PC ← ALUOut` | ALUSrcA=1, ALUSrcB=00, ALUOp=01, PCWriteCond, PCSource=01 |
| j | `PC ← {PC[31:28], IR[25:0], 00}` | PCWrite, PCSource=10 |

### Etapa 4 — Memory Access ou R-type Completion

| Instrução | Operação |
|---|---|
| lw | `MDR ← Memory[ALUOut]` — sinais: MemRead, IorD=1 |
| sw | `Memory[ALUOut] ← B` — sinais: MemWrite, IorD=1 |
| R-type | `Reg[IR[15:11]] ← ALUOut` — sinais: RegWrite, RegDst=1, MemtoReg=0 |

### Etapa 5 — Memory Read Completion (apenas load)
```
Reg[IR[20:16]] ← MDR
```
Sinais: RegWrite, RegDst=0, MemtoReg=1.

## CPI por Classe de Instrução

| Instrução | Ciclos |
|---|---|
| load (lw) | 5 |
| store (sw) | 4 |
| R-type | 4 |
| branch (beq) | 3 |
| jump (j) | 3 |

**CPI médio (SPECINT2000):**
```
CPI = 0.25×5 + 0.10×4 + 0.52×4 + 0.11×3 + 0.02×3 ≈ 4.12
```

## Controle: Máquina de Estados Finita (FSM)

A unidade de controle é uma FSM com ~12 estados. Entradas: Op[5:0]. Saídas: todos os sinais de controle.

- Estados 0–1: fetch + decode (comuns a todas instruções)
- Estado 1 ramifica para lw/sw, R-type, beq, j
- Cada estado = 1 ciclo de clock
- Transições dependem do opcode após decode

Representação alternativa: [[Microprogramação]] (5.7) — controle como microinstruções simbólicas.

## Relações SPO

| Sujeito | Predicado | Objeto |
|---|---|---|
| Multiciclo | compartilha | 1 memória + 1 ULA entre instruções |
| IR | persiste | instrução por todos os ciclos dela |
| FSM | sequencia | etapas de controle por instrução |
| beq | completa em | 3 ciclos (vs 5 do lw) |
| ALUOut | armazena | branch target na etapa 2 |

## Ver também

- [[Caminho de Dados — MIPS]] — componentes do datapath
- [[Implementação Monociclo]] — alternativa com ciclo único fixo
- [[Microprogramação]] — outra representação do controle multiciclo
- [[Pipeline]] — sobreposição de múltiplas instruções
- [[Exceções MIPS]] — extensão da FSM para tratar exceções
