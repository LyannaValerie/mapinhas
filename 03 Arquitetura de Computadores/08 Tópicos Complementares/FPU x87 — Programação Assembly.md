---
title: FPU x87 — Programação Assembly
aliases:
  - FPU assembly
  - x87 assembly
  - coprocessador numérico
  - ST0
  - FLD
  - FST
  - FADD
  - FMUL
  - FCOM
  - FSTSW
  - SAHF
tags:
  - computação/arquitetura
date: 2026-04-24
source: "PC Assembly Language — Paul A. Carter (2019)"
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# FPU x87 — Programação Assembly

## Definição
O coprocessador numérico x87 (integrado ao processador desde o 486DX) tem 8 registradores de ponto flutuante de 80 bits (precisão estendida) organizados como **pilha**. ST0 é sempre o topo. Todos os mnemônicos começam com `F`.

## Relações (SPO)
- FPU x87 → organiza registradores como → [[Pilha]]
- ST0 → é → topo da pilha de registradores FPU
- FLD → empilha → valor de memória ou registrador em ST0
- FST/FSTP → armazena → ST0 em memória (FSTP desempilha)
- FCOM → compara → ST0 com src e seta → bits C0/C1/C2/C3
- FSTSW + SAHF → transfere → bits de status FPU para [[Registradores x86#Registrador de Flags|FLAGS]]
- Valor de retorno FP em C → usa → ST0 ([[Convenção de Chamada cdecl]])

---

## Registradores da Pilha FPU

8 registradores de 80 bits: **ST0, ST1, ST2, …, ST7**.

- ST0 = topo da pilha (onde todas as operações ocorrem por padrão)
- Novo valor empilhado → ST0 recebe o valor, ST1 passa para onde ST0 estava
- Valores existentes descem na pilha quando ST0 recebe novo valor

Registradores armazenam sempre em **precisão estendida** (80 bits), mesmo que o dado em memória seja `float` (32 bits) ou `double` (64 bits).

---

## Carregamento (Load)

```nasm
FLD  src    ; empilha float/double/extended da memória ou registro FPU
FILD src    ; empilha inteiro (word/dword/qword) convertido para FP
FLD1        ; empilha constante 1.0
FLDZ        ; empilha constante 0.0
FLDPI       ; empilha π
```

Tamanhos de `src` em memória:
- `dword` → single precision (float)
- `qword` → double precision (double)
- `tword` → extended precision

---

## Armazenamento (Store)

```nasm
FST   dest   ; copia ST0 → dest (memória: dword ou qword; ou reg FPU)
FSTP  dest   ; copia ST0 → dest e desempilha ST0
FIST  dest   ; ST0 → inteiro arredondado → dest (word ou dword)
FISTP dest   ; ST0 → inteiro → dest e desempilha (word, dword ou qword)
FXCH  STn    ; permuta ST0 e STn
FFREE STn    ; marca STn como vazio
```

> [!note] Controle de arredondamento
> Por padrão, FIST/FISTP arredondam para o inteiro mais próximo. Modificar via `FSTCW`/`FLDCW`.

---

## Aritmética

### Adição
```
FADD  src          ; ST0 += src  (src: reg FPU ou memória float/double)
FADD  dest, ST0    ; dest += ST0  (dest: reg FPU)
FADDP dest [,ST0]  ; dest += ST0, desempilha ST0
FIADD src          ; ST0 += (float)src  (src: word ou dword em memória)
```

### Subtração
```
FSUB  src          ; ST0 -= src
FSUBR src          ; ST0 = src - ST0  (ordem inversa)
FSUB  dest, ST0    ; dest -= ST0
FSUBR dest, ST0    ; dest = ST0 - dest
FSUBP dest         ; dest -= ST0, pop
FSUBRP dest        ; dest = ST0 - dest, pop
FISUB  src         ; ST0 -= (float)src
FISUBR src         ; ST0 = (float)src - ST0
```

### Multiplicação e Divisão
```
FMUL  src          ; ST0 *= src
FMULP dest         ; dest *= ST0, pop
FIMUL src          ; ST0 *= (float)src
FDIV  src          ; ST0 /= src
FDIVR src          ; ST0 = src / ST0
FDIVP dest         ; dest /= ST0, pop
FDIVRP dest        ; dest = ST0 / dest, pop
FIDIV  src         ; ST0 /= (float)src
FIDIVR src         ; ST0 = (float)src / ST0
```

---

## Comparação

As instruções de comparação FPU setam bits **C0, C1, C2, C3** no registrador de status FPU — **não** o registrador FLAGS da CPU.

```nasm
FCOM   src    ; compara ST0 e src (memória float/double ou reg FPU)
FCOMP  src    ; compara ST0 e src, desempilha
FCOMPP        ; compara ST0 e ST1, desempilha duas vezes
FICOM  src    ; compara ST0 e (float)src  (word ou dword)
FICOMP src    ; idem, desempilha
FTST          ; compara ST0 com 0.0
```

---

## Padrão de Comparação — FSTSW + SAHF

Para usar as flags FPU com instruções de desvio condicional (que operam sobre FLAGS da CPU):

```nasm
FSTSW dest    ; copia status word FPU → AX ou memória word
SAHF          ; AH → FLAGS (transfere C0→CF, C2→PF, C3→ZF)
LAHF          ; FLAGS → AH (inverso de SAHF)
```

> [!important] Bits transferidos como comparação **sem sinal**
> Após FSTSW AX + SAHF, usar instruções de desvio de **inteiros sem sinal**: JA, JNA, JE, JNE — não JG/JL (que são para inteiros com sinal).

```nasm
; if ( x > y )
fld   qword [x]      ; ST0 = x
fcomp qword [y]      ; compara ST0 e y, desempilha
fstsw ax             ; AX = status FPU
sahf                 ; AH → FLAGS
jna   else_part      ; JNA = "not above" → x não é > y → vai para else
then_part:
    ; código do then
    jmp end_if
else_part:
    ; código do else
end_if:
```

---

## Exemplo — Somar Array de Doubles

```nasm
segment .bss
array  resq SIZE
sum    resq 1

segment .text
    mov   ecx, SIZE
    mov   esi, array
    fldz                    ; ST0 = 0.0
lp:
    fadd  qword [esi]       ; ST0 += *(esi)
    add   esi, 8            ; próximo double (8 bytes)
    loop  lp
    fstp  qword sum         ; armazena resultado e desempilha
```

> [!warning] Especificar tamanho do operando de memória
> `fadd [esi]` é ambíguo — o assembler não sabe se é float (dword) ou double (qword). Sempre usar `fadd dword [esi]` ou `fadd qword [esi]`.

---

## Histórico

| Modelo | Coprocessador |
|---|---|
| 8086/8088 | 8087 (externo) |
| 80286 | 80287 (externo) |
| 80386 | 80387 (externo) |
| 80486DX | Integrado |
| Pentium+ | Sempre integrado |

Sistemas sem hardware FPU executam as instruções via **emulação por software** (muito mais lento).

---

## Ver também
- [[Registradores x86]]
- [[Ponto Flutuante — Operações]]
- [[Pilha]]
- [[Convenção de Chamada cdecl]]
- [[NASM — Sintaxe e Diretivas]]
- [[FPU]]
