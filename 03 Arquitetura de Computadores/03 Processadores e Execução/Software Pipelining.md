---
title: Software Pipelining
aliases:
  - software pipelining
  - modulo scheduling
  - symbolic loop unrolling
  - initiation interval
  - rotating registers
tags:
  - computação/arquitetura
  - computação/ILP
date: 2026-04-22
source: "P&H Apêndice H §H.3"
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Software Pipelining

Técnica de reorganização de loops onde cada iteração do loop **pipelined** combina instruções de diferentes iterações originais, permitindo overlap entre operações de iterações distintas.

## Relações (SPO)

| Sujeito | Predicado | Objeto |
|---|---|---|
| software pipelining | é forma de | "loop unrolling simbólico" |
| software pipelining | explora | [[Paralelismo em Nível de Loop]] |
| initiation interval | limita | throughput do kernel |
| modulo scheduling | aloca | operações a slots de tempo (mod II) |
| rotating registers | suportam | software pipelining em [[IA-64 e EPIC|IA-64]] |
| kernel | é | steady-state do loop pipelined |
| prologue/epilogue | são | código de startup/drain ao redor do kernel |

## Ideia Central

Em vez de unrolling literal (N cópias do loop body), o compilador:
1. **Analisa** dependências e latências
2. **Sobrepõe** operações de iterações consecutivas
3. **Cria kernel** que, executado repetidamente, processa uma iteração por ciclo (no steady-state)

```
Iteração original: LOAD → ADD → STORE  (3 ciclos sequenciais)

Software pipelined:
Ciclo 1:  LOAD[i]
Ciclo 2:  LOAD[i+1], ADD[i]
Ciclo 3:  LOAD[i+2], ADD[i+1], STORE[i]  ← kernel (estado estável)
Ciclo 4:  LOAD[i+3], ADD[i+2], STORE[i+1]
...
```

## Initiation Interval (II)

Número de ciclos entre início de iterações consecutivas no kernel. Limitado por:

```
II ≥ max(
    ResourceBound  = ceil(total_ops / unidades_funcionais),
    RecurrenceBound = comprimento do ciclo de dependência loop-carried
)
```

II mínimo possível = `II_min`. O compilador tenta atingir `II_min`.

## Modulo Scheduling

Algoritmo para alocar operações ao schedule:

1. Para cada operação `op` com tempo de emissão `t`:
   - Slot alvo = `t mod II`
   - Se slot ocupado → incrementa `t` e tenta novamente
2. Operações do kernel são escalonadas em janela de `II` ciclos
3. Registradores são renomeados para evitar conflitos entre iterações sobrepostas

## Prologue e Epilogue

- **Prologue:** "enche" o pipeline — executa início de iterações antes do steady-state
- **Kernel:** steady-state, executa `N - 2*(startup_count)` vezes
- **Epilogue:** "esvazia" o pipeline — completa iterações em andamento após o último LOAD

Para loops curtos, o overhead de prologue/epilogue pode superar o ganho.

## Rotating Registers (IA-64)

Problema: software pipelining cria N cópias lógicas de cada variável (uma por iteração sobreposta). Solução manual = muitas instruções de cópia explícita.

[[IA-64 e EPIC]] adicionou **rotating registers**: um ponteiro de base (`CFM` — Current Frame Marker) avança automaticamente a cada iteração do kernel, fazendo renaming implícito. Elimina as cópias explícitas.

## Vantagens e Limitações

| Aspecto | Detalhe |
|---|---|
| Throughput ideal | 1 iteração por II ciclos (sem overhead de loop control) |
| Loops curtos | Overhead domina — pode ser contraproducente |
| Corpo com desvios | Mais difícil de aplicar; requer [[Instruções Predicadas]] |
| Dependências complexas | Recorrências longas aumentam II, reduzem ganho |
| Compiladores | Implementação complexa; IA-64 motivou suporte em hardware |

## Ver também

- [[Paralelismo em Nível de Loop]] — pré-requisito: detectar se loop é paralelizável
- [[VLIW — Very Long Instruction Word]] — usa software pipelining como técnica principal
- [[IA-64 e EPIC]] — suporte em hardware a rotating registers
- [[Escalonamento Global de Código]] — técnica complementar para loops com desvios internos
- [[Instruções Predicadas]] — usadas para eliminar desvios dentro do loop body
