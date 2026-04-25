---
title: Preservação de Registradores em Assembly
aliases:
  - caller preservation
  - callee preservation
  - preservação de registradores
  - register saving
  - salvar registradores
tags:
  - computação/arquitetura
date: 2026-04-24
source: "The Art of Assembly Language — Randall Hyde (HLA ed.), Cap. 5"
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Preservação de Registradores em Assembly

## Definição
Ao chamar um procedimento, registradores podem ser sobrescritos. A estratégia de preservação define quem é responsável por salvar e restaurar os valores: o **chamado** (callee) ou o **chamador** (caller).

## Relações (SPO)
- Preservação pelo chamado → utiliza → `push`/`pop` dentro do procedimento
- Preservação pelo chamador → utiliza → `push`/`pop` em torno de cada `call`
- [[Convenção de Chamada cdecl]] → especifica → quais registradores o callee deve preservar

---

## Estratégias

### Preservação pelo chamado (Callee Saves)

O procedimento empurra na pilha todos os registradores que modifica e os restaura antes do `ret`.

```hla
procedure PrintSpaces;
begin PrintSpaces;
    push( ecx );           // salva ECX antes de usar
    mov( 40, ecx );
    repeat
        mov( ' ', al );
        stdout.putc( al );
        dec( ecx );
    until( ecx = 0 );
    pop( ecx );            // restaura ECX
end PrintSpaces;
```

**Vantagens:**
- Código chamador simples — não precisa se preocupar com quais registradores são afetados
- Uma única cópia das instruções push/pop (dentro do procedimento)

**Desvantagens:**
- Pode preservar registradores desnecessariamente — o chamador pode não precisar do valor

---

### Preservação pelo chamador (Caller Saves)

O código que chama o procedimento empurra e restaura os registradores que precisa manter.

```hla
begin callerPreservation;
    mov( 10, ecx );
    repeat
        push( ecx );       // salva ECX antes da chamada
        PrintSpaces();
        pop( ecx );        // restaura ECX após a chamada
        stdout.put( '*', nl );
        dec( ecx );
    until( ecx = 0 );
end callerPreservation;
```

**Vantagens:**
- Não preserva registradores que o chamador não usa — evita push/pop desnecessários
- Granularidade: cada site de chamada salva apenas o que realmente precisa

**Desvantagens:**
- Requer push/pop em **cada** site de chamada — código maior e mais difícil de manter
- Se o procedimento mudar os registradores que usa, todos os sites de chamada precisam ser revisados

---

## Comparação

| Aspecto | Callee Saves | Caller Saves |
|---|---|---|
| Localização do código | Dentro do procedimento | Em torno de cada `call` |
| Número de cópias de push/pop | Uma | Uma por site de chamada |
| Risco de omissão | Baixo | Alto (fácil esquecer um site) |
| Redundância | Possível (salva o que não é necessário) | Evitável |
| Manutenibilidade | Alta | Baixa em código grande |

> [!note] Convenção ABI
> A [[Convenção de Chamada cdecl]] (Linux x86) define: EBX, ESI, EDI, EBP → callee-saved; EAX, ECX, EDX → caller-saved (podem ser destruídos pelo callee sem aviso).

---

## Push/Pop de Memória

O 80x86 permite push e pop de variáveis em memória além de registradores:

```hla
push( [ebp - 8] );     // empurra variável local na pilha
// ... chamada ...
pop( [ebp - 8] );      // restaura variável local
```

---

## Ver também
- [[Pilha x86]]
- [[Procedimentos em HLA]]
- [[Convenção de Chamada cdecl]]
- [[Registradores x86]]
- [[Linguagem Assembly]]
