---
title: Processador 8088
aliases:
  - Intel 8088
  - 8088
  - as88
  - IBM PC original
  - 8088 ISA
  - registradores 8088
  - segmentação 8088
tags:
  - computação/arquitetura
date: 2026-04-13
---

# Processador 8088

## Definição

Processador de 16 bits da Intel (1979), base do IBM PC original. Antecessor direto da linha x86 moderna — todo Core i7 executa programas 8088. Usado como modelo pedagógico em cursos de assembly por sua simplicidade relativa.

## Relações (SPO)
- Intel 8088 → é ancestral de → arquitetura x86 moderna
- 8088 → tem → 14 registradores de 16 bits
- 8088 → acessa → até 1 MB de memória via segmentação
- Endereço físico → calculado por → segmento × 16 + deslocamento
- Registradores gerais → divididos em → metades de 8 bits (AH/AL, BH/BL, CH/CL, DH/DL)

---

## Registradores

O 8088 tem **14 registradores de 16 bits**, divididos em grupos funcionais:

### Registradores Gerais

| Registrador | Nome | Uso especial |
|---|---|---|
| **AX** (AH:AL) | Acumulador | Destino implícito de MUL/DIV; resultado de operações |
| **BX** (BH:BL) | Base | Pode conter ponteiro para memória: `MOV AX,(BX)` |
| **CX** (CH:CL) | Contador | Decrementado automaticamente por LOOP; CL = contagem de shifts |
| **DX** (DH:DL) | Dados | Par com AX em operações de 32 bits (DX:AX = parte alta:baixa) |

Cada registrador de 16 bits é acessível como dois registradores de 8 bits (ex: AX = AH + AL).

### Registradores de Ponteiro e Índice

| Registrador | Nome | Função |
|---|---|---|
| **SP** | Stack Pointer | Aponta para o topo da pilha; decrementado por PUSH, incrementado por POP |
| **BP** | Base Pointer | Aponta para base do quadro de pilha corrente; endereça variáveis locais |
| **SI** | Source Index | Índice de origem; autoincremento em instruções de cadeia |
| **DI** | Destination Index | Índice de destino; autoincremento em instruções de cadeia |
| **IP** | Instruction Pointer | Contador de programa; aponta para a próxima instrução |

### Registradores de Segmento

| Registrador | Segmento | Usado por |
|---|---|---|
| **CS** | Código | IP — busca de instruções |
| **DS** | Dados | BX, SI, DI — acesso a dados |
| **SS** | Pilha | SP, BP — operações de pilha |
| **ES** | Extra | DI em instruções de cadeia |

### Registrador de Flags (CC)

| Flag | Significado |
|---|---|
| **Z** | Zero — resultado é zero |
| **S** | Sinal — resultado é negativo |
| **V/O** | Overflow — excesso; resultado não cabe no número de bits |
| **C** | Carry — vai-um no bit mais à esquerda |
| **A** | Vai-um auxiliar (bit 3 → bit 4) |
| **P** | Paridade |
| **I** | Habilita interrupções |
| **T** | Modo de rastreamento (trace) |
| **D** | Direção de instruções de cadeia (0=incremento, 1=decremento) |

---

## Modelo de Memória e Segmentação

O 8088 tem registradores de 16 bits mas endereça **1 MB** (20 bits). Solução: segmentação.

### Cálculo do endereço físico

$$\text{endereço físico} = \text{registrador de segmento} \times 16 + \text{deslocamento (16 bits)}$$

Equivale a deslocar o registrador de segmento 4 bits à esquerda e somar o deslocamento.

**Exemplo:** DS = 7, BX = 12 → endereço = 7 × 16 + 12 = **124**

### Quatro segmentos

Cada segmento tem **64 KB** (65.536 bytes). Na prática, DS = SS (dados e pilha compartilham segmento); dados na parte baixa, pilha cresce do topo para baixo.

```
Memória 1 MB
┌────────────────────┐
│   Segmento extra   │ ← ES
│   Segmento pilha   │ ← SS:SP (cresce ↓)
│   Segmento dados   │ ← DS:BX/SI
│   Segmento código  │ ← CS:IP
└────────────────────┘
```

---

## Modos de Endereçamento

| Modo | Sintaxe | Endereço calculado |
|---|---|---|
| Registrador | `AX`, `BX`... | O registrador em si |
| Imediato | `#20`, `'A'` | Constante na instrução |
| Direto | `(rótulo)` | DS + deslocamento fixo |
| Indireto de registrador | `(BX)`, `(SI)`, `(DI)` | DS + conteúdo do registrador |
| Deslocamento de registrador | `20(BX)`, `fmt(SI)` | DS + registrador + constante |
| Registrador com índice | `(BX)(SI)`, `(BX)(DI)` | DS + BX + SI/DI |
| Ponteiro de base | `(BP)`, `4(BP)` | SS + BP + deslocamento |
| Ponteiro de base com índice | `(BP)(SI)`, `2(BP)(DI)` | SS + BP + SI/DI + deslocamento |

Endereços que usam **BP** pertencem ao segmento de **pilha** (SS). Todos os outros ao segmento de **dados** (DS).

---

## Conjunto de Instruções — Principais Grupos

### Movimentação de dados
| Instrução | Função |
|---|---|
| `MOV dst, src` | Copia src para dst |
| `MOVB dst, src` | Versão de byte |
| `XCHG reg, ea` | Troca conteúdo |
| `LEA reg, ea` | Carrega o **endereço** (não o conteúdo) do ea em reg |
| `PUSH ea` / `POP ea` | Empilha/desempilha palavra |
| `PUSHF` / `POPF` | Empilha/desempilha flags |

### Aritmética
| Instrução | Função |
|---|---|
| `ADD`, `ADC` | Soma (ADC inclui carry anterior) |
| `SUB`, `SBB` | Subtração (SBB subtrai carry) |
| `MUL ea` / `IMUL ea` | Multiplica AX por ea (sem/com sinal); resultado em DX:AX |
| `DIV ea` / `IDIV ea` | Divide DX:AX por ea; quociente em AX, resto em DX |
| `INC ea` / `DEC ea` | Incrementa/decrementa (não afeta carry) |
| `NEG ea` | Nega em complemento de 2 |
| `CBW` / `CWD` | Estende sinal byte→palavra / palavra→dupla |

### Lógica e deslocamento
| Instrução | Função |
|---|---|
| `AND`, `OR`, `XOR` | Operações bit a bit |
| `NOT ea` | Complemento lógico |
| `SHL`/`SAL`, `SHR`, `SAR` | Deslocamento lógico/aritmético |
| `ROL`, `ROR`, `RCL`, `RCR` | Rotação (com/sem carry) |
| `TEST ea, src` | AND sem armazenar (ajusta flags) |
| `CMP ea, src` | SUB sem armazenar (ajusta flags) |

### Controle de fluxo
| Instrução | Condição |
|---|---|
| `JMP ea` | Incondicional |
| `JE`/`JZ` | ZF=1 |
| `JNE`/`JNZ` | ZF=0 |
| `JL`/`JNGE` | SF≠OF |
| `JLE`/`JNG` | SF≠OF ou ZF=1 |
| `JG`/`JNLE` | SF=OF e ZF=0 |
| `JB`/`JC` | CF=1 (sem sinal: abaixo) |
| `JA`/`JNBE` | CF=0 e ZF=0 (sem sinal: acima) |
| `LOOP rótulo` | Decrementa CX; salta se CX > 0 |
| `CALL ea` | Empilha IP; salta |
| `RET [n]` | Desempilha IP; opcionalmente limpa n bytes da pilha |

> [!note] Signed vs. unsigned nos saltos condicionais
> Use JG/JL/JGE/JLE para operandos com sinal; JA/JB/JAE/JBE para operandos sem sinal.

### Instruções de cadeia (string)
Operam sobre sequências de bytes/palavras; SI aponta para origem (DS), DI para destino (ES); direção controlada pelo flag D.

| Instrução | Função |
|---|---|
| `MOVSB`/`MOVSW` | Move byte/palavra de (SI) para (DI); autoincrementa ambos |
| `LODSB`/`LODSW` | Carrega (SI) em AL/AX; autoincrementa SI |
| `STOSB`/`STOSW` | Armazena AL/AX em (DI); autoincrementa DI |
| `SCASB`/`SCASW` | Compara AL/AX com (DI); ajusta flags; autoincrementa DI |
| `CMPSB`/`CMPSW` | Compara (SI) com (DI); ajusta flags; autoincrementa ambos |

Prefixos de repetição: `REP` (repete CX vezes), `REPZ`/`REPE` (até ZF=0), `REPNZ`/`REPNE` (até ZF=1).

---

## Convenção de Chamada de Sub-rotina

```
; Quem chama (caller):
PUSH arg2        ; argumentos em ordem inversa
PUSH arg1
CALL subrotina
ADD SP, 4        ; limpa argumentos (2 × 2 bytes)

; Cabeçalho da sub-rotina (callee):
subrotina:
PUSH BP          ; salva BP antigo
MOV BP, SP       ; BP aponta para topo da pilha

; Acesso na pilha via BP:
; BP+0 = BP antigo
; BP+2 = endereço de retorno
; BP+4 = arg1
; BP+6 = arg2

; Variáveis locais:
SUB SP, 4        ; reserva 2 variáveis locais de 2 bytes
; BP-2 = var1
; BP-4 = var2

; Retorno:
MOV SP, BP       ; limpa variáveis locais
POP BP           ; restaura BP
RET
```

Registradores **AX e DX** podem ser modificados pela callee. Outros registradores devem ser salvos/restaurados pela callee se modificados.

---

## Assembler as88

Assembler baseado no Amsterdam Compiler Kit (ACK). Sintaxe orientada a UNIX.

### Seções
```asm
.SECT .TEXT    ; instruções
.SECT .DATA    ; dados inicializados
.SECT .BSS     ; dados não inicializados (zerados)
```

### Pseudoinstruções de dados

| Pseudoinstrução | Função |
|---|---|
| `.BYTE v1, v2` | Aloca bytes |
| `.WORD v1, v2` | Aloca palavras de 16 bits |
| `.LONG v1` | Aloca longos de 32 bits |
| `.ASCII "str"` | Cadeia sem byte zero |
| `.ASCIZ "str"` | Cadeia com byte zero (`\0`) |
| `.SPACE n` | Avança ponteiro de local n bytes (BSS) |
| `.ALIGN n` | Alinha a fronteira de n bytes |
| `.EXTERN id` | Símbolo externo para o ligador |

### Símbolos e expressões
- Constantes: `NOME = expressão`
- Rótulos globais: `rótulo:` (únicos no arquivo)
- Rótulos locais (seção TEXT): dígito único + `:` (ex: `1:`) — podem repetir; referenciados como `1f` (próximo adiante) ou `1b` (próximo atrás)
- Agrupamento em expressões: colchetes `[` `]` (não parênteses — estes indicam derreferência)

### Chamadas de sistema (SYS)

Passagem de argumentos em ordem inversa + número da chamada → instrução `SYS`.

```asm
; write(1, buf, 12) — escreve 12 bytes de buf em stdout
PUSH 12          ; nbytes
PUSH buf         ; endereço do buffer
PUSH 1           ; descritor de arquivo (stdout)
PUSH 4           ; _WRITE = 4
SYS
ADD SP, 8        ; limpa pilha (4 × 2 bytes)
; AX = bytes escritos
```

Chamadas disponíveis: `_OPEN` (5), `_CREAT` (8), `_READ` (3), `_WRITE` (4), `_CLOSE` (6), `_LSEEK` (19), `_EXIT` (1), `_GETCHAR` (117), `_PUTCHAR` (122), `_PRINTF` (127), `_SPRINTF` (125), `_SSCANF` (121).

---

## Ver também
- [[Linguagem Assembly]] — conceitos gerais de assembly, pseudoinstruções, assembler
- [[Macros Assembly]] — macros no assembler
- [[ISA]] — arquitetura do conjunto de instruções em geral
- [[Registrador]] — registradores em geral
- [[Pilha]] — mecanismo de pilha, quadros de ativação
- [[Processo de Montagem]] — duas passagens do assembler
- [[Ligador]] — ligação de módulos-objeto
- [[Intel x86]] — evolução do 8088 para arquitetura x86 moderna
