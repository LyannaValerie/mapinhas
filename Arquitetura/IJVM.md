---
title: IJVM
aliases:
  - Integer Java Virtual Machine
  - IJVM ISA
  - ijvm
tags:
  - computação/arquitetura
date: 2026-04-14
---

# IJVM

ISA pedagógica criada por Tanenbaum em *Structured Computer Organization* (Cap. 4–5) para ensinar microarquitetura e nível ISA. Subconjunto da JVM restrito a inteiros — elimina tipagem, GC e objetos; preserva o modelo de pilha e os opcodes reais da JVM.

Implementada pelos modelos [[Microprogramação|Mic-1]] a Mic-4 via microprogramação.

## Relações (SPO)
- IJVM → subconjunto simplificado de → JVM
- IJVM → implementada por → [[Microprogramação|Mic-1]] (microprogramado)
- IJVM → opera sobre → arquitetura de pilha (stack-based)
- IJVM → todos os valores são → inteiros de 32 bits
- IJVM → mesmos opcodes de → JVM real (compatibilidade de bytes)
- [[Microprogramação|JMPC]] → despacha → microrotina por opcode IJVM

---

## Estado Visível ao Programador

| Registrador | Descrição |
|---|---|
| **PC** | Program Counter — endereço (byte) da próxima instrução na área de método |
| **SP** | Stack Pointer — aponta para o topo da pilha de operandos (em palavras) |
| **LV** | Local Variable pointer — base do frame atual; aponta para OBJREF |
| **CPP** | Constant Pool Pointer — base do pool de constantes |

> [!note] Sem registradores de propósito geral
> IJVM é puramente baseada em pilha. Não há registradores de trabalho expostos ao programador — todas as operações leem e escrevem a pilha.

---

## Modelo de Memória

Quatro regiões distintas:

| Região | Apontador | Conteúdo |
|---|---|---|
| **Constant Pool** | CPP | Constantes de 32 bits + ponteiros para métodos |
| **Local Variable Frame** | LV | OBJREF + parâmetros + variáveis locais do método atual |
| **Operand Stack** | SP | Pilha de operandos (cresce para cima, acima do frame) |
| **Method Area** | PC | Bytecode dos métodos |

### Layout do Frame de Método

```
LV →  [word 0]  OBJREF    ← referência ao objeto (equivale a "this")
      [word 1]  param 1
      [word 2]  param 2
         ...
      [word N]  var local N
                ─────────── link area (criada por INVOKEVIRTUAL)
                old PC     ← PC do chamador
                old LV     ← LV do chamador
SP →  [topo]   pilha de operandos
```

---

## Conjunto de Instruções

### Convenções de formato

| Campo | Tamanho |
|---|---|
| `byte` | 1 byte (signed) |
| `varnum` | 1 byte — índice de variável local (2 bytes com WIDE) |
| `const` | 1 byte signed (2 bytes com WIDE) |
| `index` | 2 bytes — índice no constant pool |
| `offset` | 2 bytes signed — deslocamento relativo ao PC da instrução de desvio |

### Tabela completa

| Mnemônico | Opcode | Operandos | Efeito |
|---|---|---|---|
| `NOP` | `0x00` | — | Nenhum |
| `BIPUSH byte` | `0x10` | byte | SP++; TOS ← byte (sign-extended para 32 bits) |
| `LDC_W index` | `0x13` | index | SP++; TOS ← CPP[index] |
| `ILOAD varnum` | `0x15` | varnum | SP++; TOS ← LV[varnum] |
| `ISTORE varnum` | `0x36` | varnum | LV[varnum] ← TOS; SP-- |
| `POP` | `0x57` | — | SP-- (descarta TOS) |
| `DUP` | `0x59` | — | SP++; TOS ← TOS anterior (duplica topo) |
| `SWAP` | `0x5F` | — | Troca os dois elementos do topo |
| `IADD` | `0x60` | — | a=pop; b=pop; push(b+a) |
| `ISUB` | `0x64` | — | a=pop; b=pop; push(b−a) |
| `IAND` | `0x7E` | — | a=pop; b=pop; push(b AND a) |
| `IOR` | `0x80` | — | a=pop; b=pop; push(b OR a) |
| `IINC varnum, const` | `0x84` | varnum, const | LV[varnum] += const (não usa pilha) |
| `IFEQ offset` | `0x99` | offset | if (pop == 0) PC += offset |
| `IFLT offset` | `0x9B` | offset | if (pop < 0) PC += offset |
| `IF_ICMPEQ offset` | `0x9F` | offset | a=pop; b=pop; if (a==b) PC += offset |
| `GOTO offset` | `0xA7` | offset | PC += offset (incondicional) |
| `IRETURN` | `0xAC` | — | Retorna inteiro do método (ver abaixo) |
| `INVOKEVIRTUAL index` | `0xB6` | index | Chama método (ver abaixo) |
| `WIDE` | `0xC4` | — | Prefixo: próxima instrução usa operandos de 16 bits |
| `ERR` | `0xFE` | — | Imprime erro e para *(extensão Tanenbaum)* |
| `HALT` | `0xFF` | — | Para execução *(extensão Tanenbaum)* |

> [!note] ERR e HALT
> Não existem na JVM real. Adicionados por Tanenbaum para simplificar programas de exemplo.

---

## Instruções de Chamada e Retorno

### `INVOKEVIRTUAL index`

`CPP[index]` aponta para a entrada do método no constant pool.

1. `n` ← número de parâmetros (inclui OBJREF); lido no cabeçalho do método
2. `new_LV` ← SP − n + 1 (aponta para OBJREF já na pilha)
3. Salva link area acima das variáveis: `mem[new_LV + n]` ← PC; `mem[new_LV + n + 1]` ← LV
4. `LV` ← `new_LV`
5. `PC` ← endereço do primeiro byte do método chamado

### `IRETURN`

1. Valor de retorno ← TOS
2. `SP` ← `LV − 1` (descarta frame inteiro, inclusive link area)
3. Restaura: `PC` ← old PC; `LV` ← old LV (da link area)
4. Push valor de retorno no frame do chamador

---

## Prefixo `WIDE`

Estende operandos da instrução seguinte para 16 bits:

| Instrução | Sem WIDE | Com WIDE |
|---|---|---|
| `ILOAD varnum` | varnum: 1 byte | varnum: 2 bytes |
| `ISTORE varnum` | varnum: 1 byte | varnum: 2 bytes |
| `IINC varnum, const` | varnum: 1 byte; const: 1 byte | varnum: 2 bytes; const: 2 bytes |

Permite acesso a frames com mais de 255 variáveis locais.

---

## Características

| Aspecto | Valor |
|---|---|
| Modelo | Stack-based |
| Largura de dados | 32 bits |
| Endianness | Big-endian (igual à JVM) |
| Tipos suportados | Apenas `int` (32 bits) |
| Ponto flutuante | Não |
| Objetos / GC | Não |
| Compatibilidade de opcodes | Sim — mesmos valores hex da JVM real |

> [!info] Por que apenas inteiros?
> Remover outros tipos elimina instruções de cast, verificação de tipo e GC — simplifica drasticamente a microarquitetura sem perder os conceitos de pilha, chamada de método e controle de fluxo.

---

## Implementação no Mic-1

Cada opcode IJVM corresponde a uma microrotina no control store do Mic-1:

- **Despacho:** mecanismo JMPC usa o byte do opcode (em MBR) para saltar diretamente à microrotina correta — tabela de despacho de 256 entradas
- **Microrotinas:** sequências de microinstruções que manipulam MAR, MDR, SP, LV, CPP, TOS, PC via ULA e barramento B
- **Escala:** ~112 microinstruções cobrem o conjunto completo IJVM no Mic-1

Ver [[Microprogramação]] para detalhes do datapath, formato de microinstrução (MAL) e mecanismo JMPC.

---

## Ver também
- [[Microprogramação]] — Mic-1/Mic-4 implementam IJVM; MAL; despacho JMPC; evolução do CPI
- [[ISA]] — IJVM como exemplo de ISA de nível 3 na hierarquia de Tanenbaum
- [[Níveis de Abstração]] — IJVM = nível ISA; Mic-1 = nível de microarquitetura
- [[Máquina Virtual]] — relação com a JVM real
- [[Pilha]] — modelo de execução stack-based; SP, frames, LIFO
- [[Caminho de Dados]] — datapath do Mic-1 que executa as microrotinas IJVM
- [[Y86-64]] — outra ISA pedagógica (baseada em x86-64, registradores, CS:APP)
