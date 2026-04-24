---
title: Exceções MIPS
tags:
  - arquitetura
  - mips
  - processador
  - controle
aliases:
  - MIPS Exceptions
  - Exceção MIPS
  - Interrupção MIPS
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Exceções MIPS

Eventos inesperados que interrompem o fluxo normal de execução. MIPS distingue entre exceção (causa interna) e interrupção (causa externa), mas trata ambas como **exceptions** (P&H Cap. 5.6).

## Terminologia

| Termo | MIPS | Causa |
|---|---|---|
| **Exception** | termo geral | qualquer fluxo excepcional (interno ou externo) |
| **Interrupt** | apenas externo | I/O, hardware externo |

> [!note] Contraste com IA-32
> Intel usa "interrupt" para todos os tipos. MIPS usa "exception" para tudo, reservando "interrupt" só para eventos externos.

## Exemplos por Origem

| Evento | Origem | Termo MIPS |
|---|---|---|
| Solicitação de dispositivo I/O | Externa | Interrupt |
| Chamada de sistema (syscall) | Interna | Exception |
| Overflow aritmético | Interna | Exception |
| Instrução indefinida | Interna | Exception |
| Falha de hardware | Ambas | Exception ou Interrupt |

## Mecanismo de Tratamento

Ao ocorrer exceção:
1. Endereço da instrução causadora salvo no **EPC** (Exception Program Counter).
2. Causa registrada no **Cause register**.
3. PC recebe endereço do handler do SO: `8000 0180hex`.

O SO então:
- Identifica a causa via Cause register (ou endereço vetorizado).
- Toma ação apropriada (terminar programa, tratar overflow, etc.).
- Opcionalmente reinicia execução usando EPC.

> [!warning] EPC ≠ PC atual
> No momento da exceção, o PC já foi incrementado para PC+4. O EPC deve armazenar PC−4 (endereço da instrução faltosa). Implementação: ULA subtrai 4 do PC e escreve no EPC.

## Registradores Adicionais

| Registrador | Tamanho | Função |
|---|---|---|
| **EPC** | 32 bits | endereço da instrução que causou a exceção |
| **Cause** | 32 bits | codifica o tipo de exceção; bit 0: 0=instrução indefinida, 1=overflow |

## Métodos de Comunicar a Causa

### 1. Cause Register (MIPS padrão)
- Único ponto de entrada para todas as exceções.
- SO lê o campo cause para determinar o tipo.

### 2. Vectored Interrupts
- Causa determina o endereço do handler.
- Exemplos:
  - Instrução indefinida: `C000 0000hex`
  - Overflow aritmético: `C000 0020hex`
- Endereços separados por 32 bytes (8 instruções) para processamento mínimo.

## Implementação no Datapath Multiciclo

Extensão da FSM com 2 novos estados (ex.: estados 10 e 11):

### Detecção
| Exceção | Detecção |
|---|---|
| Instrução indefinida | nenhuma transição válida do estado 1 para o opcode lido |
| Overflow aritmético | sinal **Overflow** da ULA asserted no estado 7 (execução R-type) |

### Ação nos novos estados
```
Cause ← tipo_exceção        (CauseWrite, IntCause)
EPC ← ALU(PC − 4)           (EPCWrite; usa ULA com ALUSrcB=01, subtract)
PC ← 8000 0180hex           (PCWrite, PCSource=11)
```

### Sinais de controle adicionados
| Sinal | Função |
|---|---|
| **EPCWrite** | habilita escrita no EPC |
| **CauseWrite** | habilita escrita no Cause register |
| **IntCause** | seta bit 0 do Cause (0=indef, 1=overflow) |
| **PCSource=11** | seleciona endereço do handler de exceção |

> [!tip] Impacto em desempenho
> Detecção de exceção frequentemente está no caminho crítico. Implementação incorreta pode aumentar o ciclo de clock ou introduzir bugs difíceis de reproduzir.

## Relações SPO

| Sujeito | Predicado | Objeto |
|---|---|---|
| EPC | armazena | endereço da instrução faltosa (PC−4) |
| Cause | registra | tipo da exceção |
| FSM | adiciona | 2 estados para tratar exceções |
| Overflow | detectado por | sinal Overflow da ULA |
| Instrução indefinida | detectada por | opcode sem transição no estado 1 |
| Handler SO | endereçado em | 8000 0180hex |

## Ver também

- [[Implementação Multiciclo]] — FSM estendida para exceções
- [[Implementação Monociclo]] — exceções no contexto monociclo
- [[Interrupção]] — conceito geral de interrupções
- [[ULA]] — gera sinal Overflow
- [[MIPS — ISA]] — EPC e Cause são registradores especiais do coprocessador 0
