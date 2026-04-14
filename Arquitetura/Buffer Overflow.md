---
title: Buffer Overflow
aliases:
  - buffer overflow
  - estouro de buffer
  - stack smashing
  - exploit
  - buffer overrun
tags:
  - computação/arquitetura
  - computação/segurança
date: 2026-04-13
---

# Buffer Overflow

## Definição
Vulnerabilidade que ocorre quando um programa escreve além dos limites de um buffer alocado na [[Pilha]] (ou heap), corrompendo dados adjacentes — inclusive o endereço de retorno. Permite a um atacante redirecionar o fluxo de execução para código malicioso.

## Relações (SPO)
- Buffer overflow → corrompe → [[Pilha#Quadro de Pilha (Stack Frame)|stack frame]]
- Buffer overflow → resulta de → ausência de verificação de limites no C
- Buffer overflow → explora → armazenamento do endereço de retorno na pilha
- ASLR → mitiga → buffer overflow por randomização de endereços
- Stack canary → detecta → corrupção do frame antes do retorno
- NX bit → impede → execução de código injetado na pilha

---

## Mecanismo

A [[Pilha]] armazena variáveis locais **junto** com o endereço de retorno. Em C, funções como `gets()`, `strcpy()`, `sprintf()` não verificam o tamanho do destino:

```c
void echo() {
    char buf[8];   /* buffer pequeno */
    gets(buf);     /* sem limite — perigoso */
    puts(buf);
}
```

Layout do stack frame de `echo`:

```
endereço de retorno  ← alvo do ataque
[bytes 23–8]         ← espaço não usado
[buf: bytes 7–0]     ← início do buffer
%rsp
```

Ao escrever uma string longa, o atacante sobrescreve o endereço de retorno com o endereço do exploit code. A instrução `ret` então salta para o código malicioso.

| Caracteres digitados | Estado |
|---|---|
| 0–7 | Nenhuma corrupção |
| 8–23 | Espaço não usado corrompido |
| 24–31 | **Endereço de retorno corrompido** |
| 32+ | Estado salvo do chamador corrompido |

---

## Exploit Code

O atacante injeta:
1. **Shellcode**: sequência de bytes que executa uma shell (`/bin/sh`) via syscall
2. **Endereço de retorno falso**: aponta para o shellcode injetado

Uma vez executado, o shellcode tipicamente chama `execve("/bin/sh", ...)`, dando ao atacante controle total do processo.

> [!example] Internet Worm de 1988
> Usou buffer overflow no daemon `fingerd`. O worm sobrescrevia o retorno com código que abria uma shell remota, permitindo replicação automática entre hosts.

---

## Defesas

### 1. ASLR — Address Space Layout Randomization

Randomiza os endereços da pilha, heap, bibliotecas e código a cada execução. O atacante não consegue determinar o endereço do shellcode com antecedência.

- **Implementação**: aloca espaço aleatório (`alloca(n)`) antes do início do programa; n varia entre 0 e ~2²³ bytes em sistemas de 32 bits, até 2³² em 64 bits
- **Limitação**: ataques de força bruta são viáveis em 32 bits (~32.768 tentativas com nop sled de 128 bytes). Em 64 bits o espaço é muito maior (~2²⁴ tentativas)

**Nop sled**: sequência de instruções `nop` (sem efeito) antes do shellcode. Se o atacante acertar qualquer endereço dentro do sled, o processador "desliza" até o código malicioso.

### 2. Stack Canary (Stack Protector)

O compilador insere um valor aleatório ("canário") entre o buffer e o endereço de retorno:

```
endereço de retorno
canário              ← valor aleatório gerado em tempo de execução
[variáveis locais]
[buffer]
%rsp
```

Antes de retornar, o código gerado verifica se o canário foi alterado. Se sim, o programa aborta com erro.

```asm
movq  %fs:40, %rax    ; lê canário de segmento somente-leitura
movq  %rax, 8(%rsp)   ; salva no frame
...
movq  8(%rsp), %rax   ; recupera do frame
xorq  %fs:40, %rax    ; compara com original
je    .ok
call  __stack_chk_fail ; canário corrompido → aborta
.ok:
ret
```

- O canário é lido de um segmento de memória marcado como somente-leitura, impedindo que o atacante sobreescreva também o original
- O gcc insere automaticamente quando detecta `char buf[]` local (sem flag `-fno-stack-protector`)

### 3. NX Bit — No-Execute

Hardware (AMD: NX; Intel: XD — eXecute Disable) marca páginas de memória como não-executáveis. A pilha fica marcada como `rw-` (leitura + escrita, sem execução). Qualquer tentativa de executar código injetado na pilha gera exceção.

- Introduzido pelo AMD na extensão de 64 bits; Intel seguiu com XD
- Implementado via bit separado na estrutura de paginação (PTE)
- O SO marca a pilha como não-executável; o hardware verifica a cada fetch de instrução

> [!note] Limitação do NX
> NX impede execução de código na pilha, mas não impede ataques **return-to-libc** (ret2libc) ou **ROP** (Return-Oriented Programming), que reutilizam código legítimo já em memória executável.

---

## Boas Práticas de Programação

| Função insegura | Alternativa segura |
|---|---|
| `gets(buf)` | `fgets(buf, sizeof(buf), stdin)` |
| `strcpy(dst, src)` | `strncpy(dst, src, n)` |
| `strcat(dst, src)` | `strncat(dst, src, n)` |
| `sprintf(buf, fmt, ...)` | `snprintf(buf, sizeof(buf), fmt, ...)` |

Toda interface que aceita entrada externa deve validar tamanho antes de copiar para buffer interno.

---

## Ver também
- [[Pilha]] — stack frame e endereço de retorno
- [[Registradores x86]] — %rsp, %rbp como ponteiros do frame
- [[Sistema de Compilação]] — gcc com `-fstack-protector` (padrão)
- [[Memória Virtual]] — NX bit implementado via PTE
- [[Proteção de Memória x86]] — mecanismo de proteção por página
- [[Convenção de Chamada x86-64]] — layout do frame e passagem de argumentos
