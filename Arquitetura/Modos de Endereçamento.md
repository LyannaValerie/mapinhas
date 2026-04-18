---
title: Modos de Endereçamento
aliases:
  - addressing modes
  - modo de endereçamento
  - endereçamento imediato
  - endereçamento direto
  - endereçamento indexado
tags:
  - computação/arquitetura
date: 2026-04-13
---

# Modos de Endereçamento

## Definição
Conjunto de regras que determinam como o campo de endereço de uma instrução [[ISA]] é interpretado para localizar o operando. Define *onde* o dado está — no próprio código da instrução, num registrador, na memória ou calculado em tempo de execução.

## Relações (SPO)
- Modo de endereçamento → faz parte de → [[Formatos de Instrução]]
- Modo de endereçamento → influencia → comprimento da instrução e ciclos de barramento
- Endereçamento imediato → operando está → dentro da própria instrução
- Endereçamento de registrador → evita → acesso à memória principal
- Endereçamento indexado → essencial para → percorrer vetores em laços
- Endereçamento indireto de registrador → implementa → ponteiros

---

## Modos Fundamentais

### Endereçamento Imediato
O campo de endereço contém o **próprio valor** do operando (constante).

```
MOV R1, #4    ; carrega a constante 4 em R1
```

- **Vantagem:** nenhum acesso extra à memória
- **Desvantagem:** só constantes; tamanho limitado pelo campo na instrução
- **Uso típico:** inicializar registradores, comparar com limites

---

### Endereçamento Direto
O campo de endereço contém o **endereço completo** da posição de memória.

```
MOV R1, [1000]   ; lê a palavra na posição 1000 da memória
```

- **Limitação:** acessa sempre o mesmo local; serve apenas para variáveis globais com endereço fixo em tempo de compilação

---

### Endereçamento de Registrador
Operando está num **registrador**. O campo de endereço especifica qual.

```
ADD R1, R2    ; soma R2 em R1 — ambos são registradores
```

- Modo mais comum em arquiteturas RISC
- Acesso muito rápido — sem acesso à memória principal
- Compiladores colocam variáveis mais usadas em registradores (ex: índice de laço)

---

### Endereçamento Indireto de Registrador
O registrador contém um **ponteiro** (endereço) para o operando na memória.

```
ADD R1, (R2)   ; R2 contém o endereço; operando é M[R2]
```

- **Grande vantagem:** percorre posições de memória distintas a cada execução (sem automodificação de código)
- Exemplo — soma de vetor:

```asm
MOV R1, #0        ; acumulador
MOV R2, #A        ; R2 aponta para início do vetor A
MOV R3, #A+4096   ; R3 = endereço após último elemento
LOOP:
  ADD R1, (R2)    ; soma M[R2] em R1
  ADD R2, #4      ; avança ponteiro
  CMP R2, R3
  BLT LOOP
```

> [!note] Programas automodificadores
> Antes desse modo existir, programas modificavam a própria instrução a cada iteração para apontar para o próximo elemento. Hoje esse padrão é considerado perigoso: quebra caches de instrução divididas, falha em espaços de endereço separados (instrução/dado) e dificulta debugging.

---

### Endereçamento Indexado
Endereço = **constante na instrução** + **valor de um registrador** (índice).

```
MOV R4, A(R2)   ; endereço = A + R2
```

- `A` é o deslocamento fixo (ponteiro base embutido na instrução)
- `R2` é o índice variável, incrementado no laço
- Útil para percorrer um vetor quando o endereço base é conhecido em tempo de compilação

---

### Endereçamento de Base Indexado
Endereço = **registrador base** + **registrador índice** + deslocamento opcional.

```
MOV R4, (R2 + R5)   ; endereço = R2 + R5
```

- Dois registradores somados como endereço
- Usado no [[Intel x86|Core i7]] com o byte SIB (Scale, Index, Base)
- Muito útil para vetores multidimensionais ou quando base e índice variam independentemente

> [!example] SIB no Core i7
> Acesso a `a[i]` dentro de um quadro de pilha onde EBP aponta para a base:
> ```
> endereço = 4 × EAX + EBP + 8
> ```
> EAX = índice `i`; EBP = base do frame; 8 = deslocamento de `a[0]` dentro do frame.

---

### Endereçamento de Pilha
Instruções sem campo de endereço explícito — operandos implícitos no topo da [[Pilha]].

```
IADD   ; retira dois valores do topo da pilha, soma, empilha resultado
```

- Formato de instrução mais curto possível (zero endereços)
- Base da [[Notação Polonesa Invertida]]
- Ver [[Pilha#Notação Polonesa Invertida e Endereçamento de Pilha]]

---

### Endereçamento Relativo ao PC
Endereço = **PC atual** + **deslocamento com sinal** na instrução.

```
BLT LOOP   ; desvia para PC + deslocamento
```

- Padrão para instruções de desvio condicional
- Código **relocável** — não depende de endereço absoluto
- Na ARM: deslocamento de 24 bits → alcance de ~±32 MB

---

## Comparação entre Arquiteturas

| Modo | [[Intel x86|Core i7]] | [[ARM]] OMAP4430 | [[AVR]] ATmega168 |
|---|---|---|---|
| Imediato | ✓ | ✓ | ✓ |
| Direto | ✓ | — | ✓ (limitado a 7 bits; 16 bits em instrução de 32 bits) |
| Registrador | ✓ | ✓ | ✓ |
| Indireto de registrador | ✓ | ✓ | ✓ (pares X, Y, Z) |
| Indexado | ✓ | ✓ (só LDR/STR) | ✓ (indireto + deslocamento) |
| Base indexado | ✓ (SIB) | ✓ (soma de dois reg.) | — |
| Relativo ao PC | ✓ | ✓ (LDR/STR + desvios) | ✓ (desvios) |

> [!tip] Princípio de projeto
> Menos modos com semântica clara supera muitos modos irregulares. O compilador precisa saber o custo exato (tempo + tamanho) de cada modo. Em geral, **imediato + direto + registrador + indexado** é suficiente para quase todas as aplicações.

---

## Modos de Endereçamento para Desvios
Instruções de desvio também precisam especificar o destino:

| Modo | Descrição | Prós/Contras |
|---|---|---|
| Direto | Endereço absoluto na instrução | Simples; campo grande necessário |
| Indireto de registrador | Destino calculado e armazenado num registrador | Flexível; origem de bugs difíceis |
| Relativo ao PC | PC + deslocamento | Código relocável; faixa limitada pelo tamanho do campo |

---

---

## Especificadores de Operando x86-64

Em x86-64 (ATT syntax), os operandos de uma instrução se dividem em três tipos e nove formas:

| Tipo | Sintaxe | Valor efetivo | Nome |
|---|---|---|---|
| Imediato | `$Imm` | `Imm` | Imediato |
| Registrador | `ra` | `R[ra]` | Registrador |
| Memória | `Imm` | `M[Imm]` | Absoluto |
| Memória | `(ra)` | `M[R[ra]]` | Indireto |
| Memória | `Imm(rb)` | `M[Imm + R[rb]]` | Base + deslocamento |
| Memória | `(rb, ri)` | `M[R[rb] + R[ri]]` | Indexado |
| Memória | `Imm(rb, ri)` | `M[Imm + R[rb] + R[ri]]` | Indexado |
| Memória | `(, ri, s)` | `M[R[ri] · s]` | Indexado com escala |
| Memória | `Imm(, ri, s)` | `M[Imm + R[ri] · s]` | Indexado com escala |
| Memória | `(rb, ri, s)` | `M[R[rb] + R[ri] · s]` | Indexado com escala |
| Memória | `Imm(rb, ri, s)` | `M[Imm + R[rb] + R[ri] · s]` | Forma geral |

**Fator de escala `s`**: deve ser 1, 2, 4 ou 8 — cobre os tamanhos dos tipos primitivos (char, short, int, long/ptr).

A forma geral `Imm(rb, ri, s)` é usada principalmente para acesso a elementos de arrays: `Imm` = deslocamento do campo, `rb` = endereço base da estrutura, `ri` = índice, `s` = tamanho do elemento.

> [!example] Exemplo
> `9(%rax, %rdx)` → `M[R[%rax] + R[%rdx] + 9]`  
> `(%rax, %rdx, 4)` → `M[R[%rax] + R[%rdx] · 4]`

Restrição importante: uma instrução **não pode** ter dois operandos de memória simultaneamente — copiar de memória para memória requer instrução intermediária via registrador.

---

## Ver também
- [[Formatos de Instrução]]
- [[ISA]]
- [[Pilha]]
- [[Registradores x86]]
- [[Convenção de Chamada x86-64]]
- [[ARM]]
- [[AVR]]
- [[RISC e CISC]]
