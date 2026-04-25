---
title: Convenção de Chamada cdecl
aliases:
  - cdecl
  - C calling convention
  - convenção de chamada C
  - prologue assembly
  - epilogue assembly
  - caller-saved
  - callee-saved
tags:
  - computação/arquitetura
date: 2026-04-24
source: "PC Assembly Language — Paul A. Carter (2019)"
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Convenção de Chamada cdecl

## Definição
Conjunto de regras que define como chamador (*caller*) e chamado (*callee*) trocam dados em x86 32 bits. É a convenção padrão de todos os compiladores C para PC. Permite subprogramas **reentrantes** — seguros de chamar em qualquer ponto, inclusive recursivamente.

## Relações (SPO)
- Convenção cdecl → define → passagem de parâmetros via [[Pilha]]
- Convenção cdecl → exige → prólogo/epílogo no subprograma
- EBP → serve como → ponteiro de frame fixo dentro do subprograma
- EAX → carrega → valor de retorno (tipo inteiro/ponteiro)
- Caller → limpa → parâmetros da pilha após CALL
- Callee → preserva → EBX, ESI, EDI, EBP (callee-saved)

---

## Regras Fundamentais

### Passagem de parâmetros
- Parâmetros empilhados da **direita para a esquerda** (ordem inversa da declaração em C)
- Parâmetros menores que 32 bits são estendidos para 32 bits antes de empilhar
- Para passar um valor por referência: empilha o **endereço** (não o valor)

### Limpeza da pilha
- **Caller** remove os parâmetros após o retorno (`add esp, N`)
- *Não* é o callee — isso distingue cdecl de pascal/stdcall

### Retorno de valores

| Tipo de retorno | Onde |
|---|---|
| Inteiros, enums, ponteiros (≤ 32 bits) | `EAX` |
| 64 bits | `EDX:EAX` (EDX = parte alta) |
| Ponto flutuante | `ST0` (topo da pilha FPU) |

---

## Prólogo e Epílogo

Todo subprograma seguindo cdecl deve executar:

```nasm
subprogram_label:
    push    ebp         ; salva EBP original na pilha
    mov     ebp, esp    ; EBP = ESP (frame pointer fixo)
    ; ... corpo do subprograma ...
    pop     ebp         ; restaura EBP original
    ret
```

### Por que usar EBP?

Após o `push ebp` + `mov ebp, esp`, o layout da pilha é fixo:

```
[EBP + 12] → 2º parâmetro
[EBP +  8] → 1º parâmetro
[EBP +  4] → endereço de retorno  (empilhado por CALL)
[EBP +  0] → EBP antigo           (empilhado pelo prólogo)
[EBP -  4] → 1ª variável local
[EBP -  8] → 2ª variável local
```

ESP pode flutuar durante o subprograma (PUSHes internos, mais variáveis locais), mas EBP permanece fixo — os offsets de parâmetros e variáveis locais nunca mudam.

---

## Variáveis Locais

Alocadas subtraindo de ESP após o prólogo:

```nasm
subprogram_label:
    push    ebp
    mov     ebp, esp
    sub     esp, 8      ; reserva 8 bytes para variáveis locais
    ; [EBP-4] = 1ª variável local
    ; [EBP-8] = 2ª variável local
    ...
    mov     esp, ebp    ; libera variáveis locais (alternativa a LEAVE)
    pop     ebp
    ret
```

Alternativa com `ENTER`/`LEAVE`:
```nasm
    enter   LOCAL_BYTES, 0  ; equivale ao prólogo completo
    ...
    leave                   ; equivale a: mov esp, ebp / pop ebp
    ret
```

---

## Registradores: Caller-Saved vs Callee-Saved

| Registradores | Responsabilidade | Motivo |
|---|---|---|
| `EAX`, `ECX`, `EDX` | **Caller-saved** | Callee pode destruí-los livremente |
| `EBX`, `ESI`, `EDI`, `EBP` | **Callee-saved** | C usa para variáveis de registro; callee deve restaurar |
| `CS`, `DS`, `SS`, `ES` | **Callee-saved** | Segmentos não devem mudar |

O callee usa a pilha para salvar os registradores que precisa modificar:

```nasm
my_func:
    push    ebp
    mov     ebp, esp
    push    ebx         ; salva EBX (callee-saved)
    push    esi         ; salva ESI
    ; ... usa EBX e ESI ...
    pop     esi         ; restaura ESI
    pop     ebx         ; restaura EBX
    pop     ebp
    ret
```

---

## Exemplo Completo de Chamada

Em C:
```c
result = my_func(a, b);
```

Em assembly (caller):
```nasm
push    dword [b]       ; 2º parâmetro (direita primeiro)
push    dword [a]       ; 1º parâmetro
call    my_func
add     esp, 8          ; caller remove 2 parâmetros (2 × 4 bytes)
mov     [result], eax   ; valor de retorno em EAX
```

---

## Calcular Endereço de Variável Local (LEA)

Para passar endereço de variável local como argumento:

```nasm
; x está em [EBP-8]
lea     eax, [ebp - 8]  ; EAX = endereço de x (LEA não lê memória)
push    eax             ; passa &x para o próximo CALL
```

`MOV eax, ebp - 8` é **inválido** — o assembler não calcula expressões com registradores. `LEA` faz esse cálculo em runtime.

---

## Outras Convenções

| Convenção | Quem limpa a pilha | Permite varargs? | Uso |
|---|---|---|---|
| `cdecl` | Caller | Sim | Padrão C (printf, scanf) |
| `stdcall` | Callee | Não | Windows API |
| `pascal` | Callee | Não | Pascal, legado |
| `regparm(n)` | Caller | Parcialmente | GCC: até 3 args em registradores |

---

## Ver também
- [[Pilha]]
- [[Registradores x86]]
- [[NASM — Sintaxe e Diretivas]]
- [[Linguagem Assembly]]
- [[Modos de Endereçamento]]
