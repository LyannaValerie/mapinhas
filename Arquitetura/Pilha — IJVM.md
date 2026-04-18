---
title: Pilha — IJVM
aliases:
  - Pilha IJVM
  - pilha de execução IJVM
  - IJVM stack
tags:
  - computação/arquitetura
date: 2026-04-18
---

# Pilha — IJVM

Estrutura de dados LIFO usada pelo processador para gerenciar chamadas de procedimento, variáveis locais e operações intermediárias.

## Relações SPO

| Sujeito | Predicado | Objeto |
|---|---|---|
| Pilha | é gerenciada por | [[Registrador|SP e LV]] |
| SP | aponta para | topo da pilha |
| LV | aponta para | base do frame atual |
| Frame | contém | link pointer, variáveis locais, pilha de operandos |
| INVOKEVIRTUAL | empilha | novo frame |
| IRETURN | desempilha | frame corrente |

## Estrutura de Frame de Procedimento

```
Memória (endereços crescentes ↓)

┌────────────────────┐
│  parâmetros        │  ← acessados por LV+offset fixo
├────────────────────┤
│  link pointer      │  ← LV aponta aqui; guarda LV do chamador
├────────────────────┤
│  variáveis locais  │  ← LV+1, LV+2, ...
├────────────────────┤
│  pilha de operandos│  ← cresce em direção a SP
│  ...               │
│  topo              │  ← SP
└────────────────────┘
```

- **LV** (Local Variable pointer): base do frame corrente; registrador dedicado.
- **SP** (Stack Pointer): topo da pilha; avança conforme operandos são empilhados.
- **Link pointer**: primeira palavra do frame; armazena LV do chamador para restauração no retorno.

## Pilha de Operandos

Modelo de execução baseado em **pilha de operandos** (operand stack): instruções não especificam operandos explicitamente — consomem e produzem valores no topo da pilha.

Exemplo — soma de dois inteiros:
```
ILOAD x   ; empilha variável local x
ILOAD y   ; empilha variável local y
IADD      ; desempilha dois, empilha resultado
```

Vantagem: instruções compactas (sem campos de registrador). Desvantagem: acesso lento se implementado literalmente em memória — processadores reais mapeiam o topo para registradores internos.

## INVOKEVIRTUAL — Chamada de Método

Ao chamar um método, o frame do chamador e do chamado se relacionam assim:

1. Parâmetros já estão na pilha de operandos do chamador.
2. `INVOKEVIRTUAL` cria novo frame:
   - salva LV atual como **link pointer** no início do novo frame
   - move LV para apontar ao link pointer do novo frame
   - reserva espaço para variáveis locais
   - SP aponta ao topo do novo frame
3. Execução continua no método chamado.

```
Antes (chamador):       Depois (chamado):
┌──────────────┐        ┌──────────────┐
│ arg1         │        │ arg1         │ ← LV_novo (link ptr) aponta antigo LV
│ arg2         │        │ arg2         │
│              │        │ var_local_1  │
│ ← SP         │        │ var_local_2  │
│ ← LV         │        │ ← SP         │
└──────────────┘        │ ← LV         │
                        └──────────────┘
```

## IRETURN — Retorno de Método

1. Valor de retorno (se houver) fica no topo da pilha.
2. `IRETURN` restaura:
   - SP ← LV − 1 (descarta frame atual + parâmetros)
   - LV ← link pointer (restaura LV do chamador)
3. Execução retorna ao chamador; valor de retorno está no topo da pilha do chamador.

## Modelo de Memória IJVM

No modelo [[IJVM]] (usada como exemplo em Tanenbaum), a memória é dividida em:

| Área | Conteúdo |
|---|---|
| Constant Pool | constantes e referências do método (somente leitura) |
| Local Variable Frame | variáveis locais + parâmetros do método atual |
| Operand Stack | pilha de operandos (parte alta do frame) |
| Method Area | bytecodes dos métodos |

O registrador **CPP** (Constant Pool Pointer) aponta à área de constantes.

> [!note] Pilha vs. Heap
> A pilha gerencia chamadas e variáveis de curta duração (alocação e liberação automáticas). O heap (não gerenciado pela pilha) armazena objetos de vida variável, gerenciados por GC ou manualmente.

## Ver também

- [[Registrador]] — SP e LV são registradores de propósito específico
- [[Microprogramação]] — Mic-1 implementa SP/LV/CPP como registradores do datapath
- [[ISA]] — modelo de pilha vs. modelo de registradores
- [[Processador]] — execução de instruções consome/produz valores na pilha
