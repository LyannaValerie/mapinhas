---
title: Convenção de Chamada x86-64
aliases:
  - calling convention
  - ABI x86-64
  - System V AMD64 ABI
  - convenção de chamada
  - caller-saved
  - callee-saved
  - argument registers
tags:
  - computação/arquitetura
date: 2026-04-13
---

# Convenção de Chamada x86-64

## Definição
Conjunto de regras que governa como chamadas de procedimento são feitas em x86-64 no Linux (System V AMD64 ABI). Define quais [[Registradores x86|registradores]] carregam argumentos, como o valor de retorno é passado, quais registradores o chamado pode destruir, e como o [[Pilha|stack frame]] é organizado.

## Relações (SPO)
- Convenção de chamada → define uso de → [[Registradores x86]]
- Convenção de chamada → organiza → [[Pilha#Quadro de Pilha (Stack Frame)|stack frame]]
- Convenção de chamada → implementada por → [[Sistema de Compilação|gcc]] automaticamente
- Convenção de chamada → parte de → ABI (Application Binary Interface)

---

## Passagem de Argumentos Inteiros e Ponteiros

Os primeiros **6** argumentos inteiros/ponteiros são passados em registradores, na ordem:

| Posição | Registrador 64 bits | 32 bits | 16 bits | 8 bits |
|---|---|---|---|---|
| 1º | %rdi | %edi | %di | %dil |
| 2º | %rsi | %esi | %si | %sil |
| 3º | %rdx | %edx | %dx | %dl |
| 4º | %rcx | %ecx | %cx | %cl |
| 5º | %r8 | %r8d | %r8w | %r8b |
| 6º | %r9 | %r9d | %r9w | %r9b |

**Argumentos 7 em diante:** passados na [[Pilha]], com o 7º no topo (offset 8 do %rsp após o endereço de retorno). Todos arredondados para múltiplo de 8 bytes.

---

## Passagem de Argumentos de Ponto Flutuante

Primeiros **8** argumentos float/double passados em [[Instruções SIMD|registradores XMM]], na ordem `%xmm0`–`%xmm7`. Argumentos adicionais vão na pilha.

---

## Valor de Retorno

| Tipo | Registrador |
|---|---|
| Inteiro / ponteiro (≤64 bits) | %rax |
| Inteiro 128 bits | %rdx:%rax |
| float / double | %xmm0 |

---

## Registradores Callee-Saved vs. Caller-Saved

### Callee-Saved (preservados pelo chamado)
O procedimento chamado **deve restaurar** estes registradores antes de retornar. Pode usá-los se salvar na pilha antes:

| Registrador | Uso convencional |
|---|---|
| %rbx | Uso geral |
| %rbp | Frame pointer (opcional) |
| %r12 | Uso geral |
| %r13 | Uso geral |
| %r14 | Uso geral |
| %r15 | Uso geral |
| %rsp | Stack pointer (sempre preservado implicitamente) |

### Caller-Saved (preservados pelo chamador)
O chamado pode **modificar livremente**. Se o chamador precisar do valor após a chamada, deve salvá-los antes de chamar:

| Registradores |
|---|
| %rax (também retorno) |
| %rcx (também 4º arg) |
| %rdx (também 3º arg) |
| %rsi (também 2º arg) |
| %rdi (também 1º arg) |
| %r8–%r11 |
| %xmm0–%xmm15 (todos caller-saved) |

> [!tip] Mnemônico
> Registradores que são argumentos (%rdi, %rsi, %rdx, %rcx, %r8, %r9) e %rax, %r10, %r11 são caller-saved — o chamado pode destruí-los. %rbx, %rbp, %r12–%r15 são callee-saved — seguros para o chamador usar após uma chamada.

---

## Layout do Stack Frame

```
Endereço alto ─── frame do chamador ────────────────
                  argumentos 7, 8, …       ← passados pelo chamador
                  endereço de retorno      ← empilhado pela instrução call
%rbp (opcional) → %rbp antigo salvo
                  registradores callee-saved salvos
                  variáveis locais
                  área de construção de argumentos   ← para chamadas deste proc.
%rsp ──────────── topo da pilha ────────────────────
```

- A pilha cresce para **endereços menores**
- `call` empurra o endereço de retorno; `ret` desempilha e salta para ele
- Muitas funções **não precisam** de frame pointer (%rbp); o gcc com `-O` o omite

---

## Alinhamento da Pilha

O valor de %rsp deve ser múltiplo de **16 bytes** imediatamente antes de qualquer instrução `call`. A instrução `call` empurra 8 bytes (endereço de retorno), tornando %rsp múltiplo de 8 na entrada da função. O prólogo tipicamente subtrai um múltiplo ímpar de 8 para restaurar o alinhamento de 16.

> [!note] SSE/AVX
> Operações SSE/AVX que leem/escrevem da memória exigem alinhamento de 16 bytes. Sem alinhamento correto, essas instruções lançam exceção. Por isso o compilador e o runtime garantem alinhamento de 16 bytes.

---

## Frame Pointer (uso opcional)

Quando a função aloca arrays de tamanho variável (via `alloca` ou VLAs), o frame pointer (%rbp) é necessário como referência fixa dentro do frame, pois %rsp não é constante. O gcc gera:

```asm
pushq %rbp        ; salva %rbp antigo
movq  %rsp, %rbp  ; %rbp = base do frame atual
...
leave             ; equivalente a: movq %rbp,%rsp; popq %rbp
ret
```

Para funções sem VLAs, o gcc moderno omite o frame pointer (flag padrão com otimização).

---

## Funções Folha (Leaf Procedures)

Funções que não chamam outras funções e cujas variáveis locais cabem em registradores **não precisam de stack frame**. O compilador evita o overhead de prólogo/epílogo neste caso.

---

## Exemplo

```c
long P(long x, long y) {
    long u = Q(y);
    long v = Q(x);
    return u + v;
}
```

```asm
P:
    pushq  %rbp          ; salva callee-saved
    pushq  %rbx
    subq   $8, %rsp      ; alinha pilha
    movq   %rdi, %rbp    ; salva x (caller destruiria %rdi na chamada a Q)
    movq   %rsi, %rdi    ; move y para 1º argumento
    call   Q
    movq   %rax, %rbx    ; salva Q(y)
    movq   %rbp, %rdi    ; restaura x como 1º argumento
    call   Q
    addq   %rbx, %rax    ; u + v
    addq   $8, %rsp
    popq   %rbx
    popq   %rbp
    ret
```

---

## Ver também
- [[Registradores x86]] — mapa completo dos registradores GPR e XMM
- [[Pilha]] — stack frames, PUSH/POP, CALL/RET
- [[Linguagem Assembly]] — instruções usadas em prólogos e epílogos
- [[Instruções SIMD]] — registradores XMM para argumentos float/double
- [[Sistema de Compilação]] — gcc gera código conforme esta convenção
