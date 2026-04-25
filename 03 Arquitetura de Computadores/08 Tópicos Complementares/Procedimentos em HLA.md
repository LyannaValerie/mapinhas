---
title: Procedimentos em HLA
aliases:
  - HLA procedures
  - procedure HLA
  - declaração de procedimento
  - retorno antecipado HLA
  - exit HLA
  - exitif HLA
tags:
  - computação/arquitetura
date: 2026-04-24
source: "The Art of Assembly Language — Randall Hyde (HLA ed.), Cap. 5"
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Procedimentos em HLA

## Definição
HLA (High-Level Assembler) implementa procedimentos com sintaxe de alto nível que compila para instruções `call`/`ret` do 80x86. Unidade básica de código reutilizável em linguagem assembly estruturada.

## Relações (SPO)
- Procedimento HLA → compila para → `call`/`ret` da [[Linguagem Assembly]]
- `call` → empurra → endereço de retorno na [[Pilha x86]]
- `ret` → desempilha → endereço de retorno e desvia para ele
- Procedimento → pode preservar → registradores via [[Preservação de Registradores em Assembly]]
- `exit`/`exitif` → transfere controle → para o fim do procedimento

---

## Declaração

```hla
procedure ProcName;
    << declarações locais >>
begin ProcName;
    << instruções do procedimento >>
end ProcName;
```

- Declarações aparecem na seção de declaração do programa (junto com `static`, `const`, `type`, etc.)
- O identificador após `procedure` deve ser o mesmo após `begin` e `end`
- Sintaticamente idêntico a um programa HLA — só muda a palavra reservada

### Exemplo concreto

```hla
procedure zeroBytes;
begin zeroBytes;
    mov( 0, eax );
    mov( 256, ecx );
    repeat
        mov( eax, [ebx] );
        add( 4, ebx );
        dec( ecx );
    until( @z );          // até ecx = 0
end zeroBytes;
```

---

## Chamada e Retorno

### Alto nível (HLA)
```hla
ProcName();             // chama o procedimento
```

### Baixo nível (80x86 puro)
```hla
call ProcName;          // empurra EIP+tamanho_instrução na pilha, salta para ProcName
```
```hla
ret;                    // desempilha endereço de retorno, desvia para ele
```

Ao atingir `end ProcName;`, o compilador HLA emite um `ret` implícito.

---

## Retorno Antecipado

`exit` e `exitif` transferem controle para o `end` do procedimento sem executar as instruções seguintes — equivalente ao `break` de loops mas para procedimentos.

```hla
exit ProcName;
exitif( expressão_booleana ) ProcName;
```

> [!note] Semântica de saída
> O nome do procedimento é exigido explicitamente para evitar ambiguidades em procedimentos aninhados. A saída vai para o `end ProcName;`, onde um `ret` é emitido.

---

## Ver também
- [[Pilha x86]]
- [[Preservação de Registradores em Assembly]]
- [[Linguagem Assembly]]
- [[Convenção de Chamada cdecl]]
- [[Modos de Endereçamento x86]]
