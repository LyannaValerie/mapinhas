---
title: Pilha x86
aliases:
  - stack x86
  - stack 80x86
  - pilha de execução x86
tags:
  - computação/arquitetura
  - computação/assembly/x86
date: 2026-04-24
source: "The Art of Assembly Language — Randall Hyde (HLA ed.), Cap. 3"
---

# Pilha x86

## Definição
Estrutura de dados **LIFO** (Last-In, First-Out) mantida pelo 80x86 no segmento de pilha. Armazena variáveis locais (`var`), informações de subrotinas e temporários. Controlada pelo registrador **ESP** (Stack Pointer).

## Relações (SPO)
- Pilha x86 → controlada por → ESP ([[Registradores x86]])
- `push` → decrementa ESP → e grava dado na memória
- `pop` → lê dado da memória → e incrementa ESP
- Pilha x86 → cresce para → endereços decrescentes (downward)
- [[Convenção de Chamada cdecl]] → usa → pilha para argumentos e endereço de retorno
- EBP → aponta para → activation record (frame da subrotina atual)
- Seção `var` (HLA) → armazenada em → pilha via `[EBP ± offset]`

## Inicialização

O OS inicializa ESP com o endereço do último byte do segmento de pilha. Dados são escritos via `push` e lidos via `pop`.

## Instrução `push`

Formas aceitas:

```asm
push( reg16 );
push( reg32 );
push( memory16 );
push( memory32 );
pushw( constante );    ; empurra word (2 bytes)
pushd( constante );    ; empurra dword (4 bytes)
```

**Operação:**
```
ESP := ESP - tamanho_operando    ; 2 ou 4 bytes
[ESP] := valor_operando
```

**Exemplo** — `push( eax )` com ESP = $00FF_FFE8:

| Antes | Depois |
|-------|--------|
| ESP = $00FF_FFE8 | ESP = $00FF_FFE4 |
| — | Mem[$00FF_FFE4] = EAX |

EAX **não é alterado** pelo `push`.

> [!warning] Sem push de byte
> `push` não aceita operandos de 8 bits. Em OS 32-bit, ESP deve permanecer múltiplo de 4 — push/pop de 16 bits isolados quebra o alinhamento e pode causar falha.

## Instrução `pop`

Formas aceitas:

```asm
pop( reg16 );
pop( reg32 );
pop( memory16 );
pop( memory32 );
```

**Operação:**
```
dest := [ESP]
ESP := ESP + tamanho_operando
```

Não existe `pop` de constante — o operando de `pop` é sempre destino (escrita).

> [!note] Dado "popped" ainda existe na memória
> `pop` apenas incrementa ESP; o byte antigo permanece na pilha até ser sobrescrito pelo próximo `push`.

## LIFO: preservação de registradores

Uso mais comum: salvar um registrador antes de sequência intermediária que o sobrescreve.

```asm
; --- sequência 1: EAX contém resultado parcial ---
push( eax );              ; salva EAX na pilha
; --- sequência 2: usa EAX para outro fim ---
pop( eax );               ; restaura EAX
; --- sequência 3: continua com EAX original ---
```

Push/pop de múltiplos registradores é LIFO estrito — a ordem de pop deve ser inversa à de push:

```asm
push( eax );
push( ebx );
push( ecx );
; ... código ...
pop( ecx );    ; primeiro saiu por último
pop( ebx );
pop( eax );
```

## Ver também
- [[Registradores x86]]
- [[Convenção de Chamada cdecl]]
- [[Modos de Endereçamento x86]]
- [[Linguagem Assembly]]
