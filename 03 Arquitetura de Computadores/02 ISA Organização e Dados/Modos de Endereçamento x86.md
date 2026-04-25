---
title: Modos de Endereçamento x86
aliases:
  - addressing modes x86
  - modos de endereçamento 80x86
  - endereçamento 80x86
tags:
  - computação/arquitetura
  - computação/assembly/x86
date: 2026-04-24
source: "The Art of Assembly Language — Randall Hyde (HLA ed.), Cap. 3"
---

# Modos de Endereçamento x86

## Definição
Formas pelas quais instruções 80x86 especificam onde estão os dados — em registrador, endereço fixo ou endereço calculado. Dominar os modos de endereçamento é passo fundamental para dominar [[Linguagem Assembly]].

## Relações (SPO)
- Modo de endereçamento → determina → como a CPU calcula o endereço efetivo
- Displacement-only → especifica → endereço fixo de 32 bits (variável estática)
- Register-indirect → usa → registrador como ponteiro para memória
- Indexed → soma → base do array + offset em registrador
- Scaled-indexed → soma → base + índice × escala (1, 2, 4 ou 8)
- [[Registradores x86]] → participam → em todos os modos exceto displacement-only
- [[Pilha x86]] → usa → register-indirect via ESP

## As cinco formas base (80386+, 32 bits)

| Forma | Sintaxe (HLA) | Endereço efetivo |
|-------|--------------|-----------------|
| Displacement-only | `mov( j, al )` | constante 32 bits |
| Register-indirect | `mov( [eax], al )` | valor em EAX |
| Displacement + base | `mov( [eax+8], al )` | EAX + constante |
| Base + indexed | `mov( [eax+ebx], al )` | EAX + EBX |
| Disp. + base + indexed (scaled) | `mov( arr[eax+ebx*4], al )` | &arr + EAX + EBX×4 |

> [!note] Modos 16-bit
> Os modos de endereçamento do 8086/8088 (com BX, SI, DI) existem mas não são suportados por HLA em OS 32-bit. Necessário apenas para MS-DOS ou modo real.

## Modo 1: Displacement-Only (Direto)

Constante 32 bits = endereço absoluto. Modo mais simples. Usado pelo compilador/HLA para variáveis `static` e `readonly`.

```asm
mov( j, al );       ; copia byte no endereço de j para AL
mov( dl, k );       ; armazena DL no endereço de k
mov( k, ax );       ; acessa word (2 bytes a partir de &k)
```

## Modo 2: Register-Indirect

O registrador contém um **ponteiro** — não o dado em si. Requer registrador de 32 bits.

```asm
; todos os oito registradores gerais são válidos:
mov( [eax], al );
mov( [ebx], al );
mov( [ecx], al );
mov( [edx], al );
mov( [edi], al );
mov( [esi], al );
mov( [ebp], al );
mov( [esp], al );
```

Não é permitido usar registradores de 8 ou 16 bits no modo indireto.

## Modo 3: Indexed (Base + Deslocamento de variável)

Endereço efetivo = endereço da variável + valor do registrador. Padrão para acesso a elementos de array.

```asm
mov( VarName[eax], al );    ; &VarName + EAX
mov( VarName[ebx], al );
```

## Modo 4: Scaled-Indexed

Endereço efetivo = base + índice × escala (1, 2, 4 ou 8). Ideal para arrays de word, dword ou qword.

```asm
mov( arr[eax*4], edx );     ; &arr + EAX×4  (array de dwords)
mov( arr[ebx*2], ax  );     ; &arr + EBX×2  (array de words)
```

> [!tip] Var section e EBP
> Variáveis declaradas em `var` (seção local) são acessadas via `[EBP ± deslocamento]` — HLA gera isso automaticamente. Por isso, `var` não pode usar scaled-indexed (EBP já está ocupado como base).

## Ver também
- [[Registradores x86]]
- [[Linguagem Assembly]]
- [[Pilha x86]]
- [[Instruções de String x86]]
- [[Convenção de Chamada cdecl]]
