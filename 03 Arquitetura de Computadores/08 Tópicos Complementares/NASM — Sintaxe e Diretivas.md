---
title: NASM — Sintaxe e Diretivas
aliases:
  - NASM
  - Netwide Assembler
  - diretivas NASM
  - segmento .data
  - segmento .bss
  - segmento .text
  - resd
  - resb
tags:
  - computação/arquitetura
date: 2026-04-24
source: "PC Assembly Language — Paul A. Carter (2019)"
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# NASM — Sintaxe e Diretivas

## Definição
NASM (Netwide Assembler) é um assembler livre para x86/x86-64 em modo protegido. Difere do MASM/TASM em sintaxe: usa colchetes explícitos para acesso à memória e nomes de segmentos em minúsculas com ponto.

## Relações (SPO)
- NASM → implementa → [[Linguagem Assembly]]
- NASM → produz → código objeto para [[Intel x86]]
- Segmento `.data` → armazena → variáveis inicializadas
- Segmento `.bss` → reserva → memória não inicializada
- Segmento `.text` → contém → instruções executáveis
- Diretiva `global` → exporta → símbolo para o [[Ligador]]
- Diretiva `extern` → importa → símbolo de outro módulo

---

## Segmentos

```nasm
segment .data       ; variáveis inicializadas
segment .bss        ; variáveis não inicializadas
segment .text       ; código executável
```

Múltiplos blocos `segment .data` e `.text` no mesmo arquivo são válidos — o ligador os combina em segmentos únicos. Útil para definir dados próximos ao código que os usa.

---

## Diretivas de Dados

### Memória inicializada — DX

| Diretiva | Unidade | Bytes |
|---|---|---|
| `db` | byte | 1 |
| `dw` | word | 2 |
| `dd` | double word | 4 |
| `dq` | quad word | 8 |
| `dt` | ten bytes | 10 |

```nasm
segment .data
x      dd  0          ; inteiro 32 bits, valor 0
array1 dd  1, 2, 3, 4 ; array de 4 dwords
msg    db  "hello", 0  ; string terminada em nulo
```

### Memória não inicializada — RESX (BSS)

| Diretiva | Unidade |
|---|---|
| `resb N` | reserva N bytes |
| `resw N` | reserva N words |
| `resd N` | reserva N double words |
| `resq N` | reserva N quad words |

```nasm
segment .bss
input  resd 1    ; reserva 1 double word
array2 resd 10   ; reserva 10 double words
```

---

## Constantes e Macros

### `equ` — constante simbólica
```nasm
SIZE equ 100      ; SIZE = 100, não pode ser redefinido
```

### `%define` — macro (preprocessor, como #define em C)
```nasm
%define SIZE 100
mov eax, SIZE     ; substituído por: mov eax, 100
```

Macros `%define` podem ser redefinidas; constantes `equ` não.

---

## Acesso à Memória

NASM usa colchetes explicitamente para referenciar memória:

```nasm
mov eax, ebx        ; EAX = EBX (registrador)
mov eax, [ebx]      ; EAX = memória em [EBX] (indireto)
mov eax, [ebx + 4]  ; EAX = memória em [EBX+4]
mov eax, 42         ; EAX = 42 (imediato)
```

> [!warning] Diferença NASM vs MASM
> MASM: `mov eax, var` lê o *valor* de `var` na memória.
> NASM: `mov eax, var` carrega o *endereço* de `var`. Para ler o valor: `mov eax, [var]`.

Quando a instrução necessita de especificação de tamanho:
```nasm
mov dword [ebx], 1    ; armazena 1 (32 bits) em [EBX]
mov byte  [ebx], 0    ; armazena 0 (8 bits)  em [EBX]
```

---

## Visibilidade de Símbolos

```nasm
global _asm_main    ; exporta _asm_main para o ligador
extern get_int      ; importa get_int de outro módulo
extern print_sum
```

- `global` torna um símbolo visível para outros módulos (obrigatório para ponto de entrada)
- `extern` declara símbolos definidos em outros arquivos
- Módulos separados são ligados pelo [[Ligador]]

---

## Prefixo de Underscore (Compatibilidade com C)

Em sistemas Windows/DJGPP: compiladores C prefixam `_` nos nomes de funções e variáveis globais.

```nasm
global _asm_main    ; corresponde a asm_main em C (Windows/DJGPP)
```

Em Linux com GCC ELF: sem prefixo `_`.

```nasm
global asm_main     ; corresponde a asm_main em C (Linux GCC)
```

---

## Include

```nasm
%include "asm_io.inc"   ; inclui arquivo (como #include em C)
```

---

## Instrução LEA — Carregar Endereço Efetivo

```nasm
lea eax, [ebp - 8]   ; EAX = endereço de (EBP - 8), NÃO lê memória
```

Usada para calcular endereços de variáveis locais na pilha antes de passá-los como ponteiros para subprogramas. Ver [[Convenção de Chamada cdecl]].

---

## Ver também
- [[Linguagem Assembly]]
- [[Macros Assembly]]
- [[Processo de Montagem]]
- [[Ligador]]
- [[Convenção de Chamada cdecl]]
- [[Registradores x86]]
- [[Modos de Endereçamento]]
