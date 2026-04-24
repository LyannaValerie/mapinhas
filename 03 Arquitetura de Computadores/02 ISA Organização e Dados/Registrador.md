---
title: Registrador
aliases:
  - registradores
  - register
tags:
  - computação/fundamentos
date: 2026-04-06
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Registrador

## Definição
Unidade de armazenamento de alta velocidade localizada **dentro do [[Processador]]** (nível de microarquitetura). Armazena um único número binário até um máximo determinado pelo seu tamanho (16, 32 ou 64 bits).

## Relações (SPO)
- Registrador → localizado em → [[Processador]] (nível 1 — microarquitetura)
- Registrador → formado por → grupo de [[Latch e Flip-Flop|flip-flops D]] (1 flip-flop por bit)
- Registrador → conectado a → [[ULA]] formando o caminho de dados
- Registrador → mais rápido que → [[Memória]] (RAM) — fisicamente dentro do processador

## Composição física

```
Transistores (MOSFET)
  → [[Portas Lógicas]] (AND, OR, NAND…)
    → [[Latch e Flip-Flop|Flip-flop D]] (memória de 1 bit)
      → Grupo de 16/32/64 flip-flops
        → Registrador
```

## Papel no caminho de dados

No nível de microarquitetura, a operação básica é:
1. Selecionar um ou dois registradores
2. A [[ULA]] opera sobre eles (ex: soma)
3. O resultado é armazenado de volta em um registrador

Um processador típico possui entre 8 e 32 registradores formando uma **memória local** do processador.

> [!tip] Por que registradores são mais rápidos?
> Estão fisicamente dentro do [[Processador]] — o caminho de comunicação com a [[ULA]] é mínimo. Ver também a tendência de integração discutida em [[Memória#Integração ao Processador]].

## Registradores de Uso Geral (GPRs)

Os **GPRs** (*General Purpose Registers* — Registradores de Uso Geral) estão disponíveis para o programador usar como quiser para armazenagem temporária dentro do processador. São necessários para instruções que exigem que valores sejam carregados antes de operar.

Exemplo x86 — soma de dois valores de 16 bits:

```asm
mov ax, 1234h   ; carrega o valor 1234h no registrador AX
add ax, 234h    ; soma 234h ao conteúdo de AX; resultado fica em AX
```

O resultado de uma instrução fica disponível no registrador para a instrução seguinte usá-lo.

## Pilha e Stack Pointer

Parte da [[Memória|RAM]] pode ser acessada como uma área temporária com comportamento **LIFO** chamada [[Pilha]]. A posição atual dentro da pilha é controlada pelo registrador especial **Stack Pointer (SP)**.

- **PUSH**: armazena um dado no topo da pilha; SP decrementado
- **POP**: extrai um dado do topo da pilha; SP incrementado

Ver [[Pilha]] para detalhes sobre CALL/RET e sub-rotinas.

## Flags

As **flags** são registradores de **1 bit** que indicam condições resultantes de operações aritméticas/lógicas. O processador agrupa todas as flags em um único registrador (ex: um registrador de 16 bits = até 16 flags).

Flags comuns: Overflow, Sign (sinal), Zero, Parity (paridade), Carry (carry/empréstimo). Em x86 o registrador de flags chama-se **EFLAGS** (32 bits) ou **RFLAGS** (64 bits). Ver [[Flag]] para o detalhamento e [[Registradores x86]] para o mapa completo.

## Acumulador

Tipo especial de registrador presente na máquina IAS (Von Neumann, 1952) e em muitos computadores históricos. Na IAS, era um registrador interno de **40 bits** dentro da [[ULA]], onde resultados de operações eram armazenados temporariamente.

> [!note] Importância histórica
> O acumulador foi o padrão dominante de registrador em computadores da primeira geração. Computadores modernos possuem conjuntos maiores de registradores de uso geral em vez de um único acumulador.

## Ver também
- [[ULA]]
- [[Processador]]
- [[Memória]]
- [[Registradores x86]] — catálogo completo GPRs/índice/ponteiro/segmento/flags/controle/debug/MM
- [[Pilha]] — Stack Pointer como registrador especial de controle da pilha
- [[Flag]] — registrador de flags como agregado de bits de status
- [[Hexadecimal]] — padding (preenchimento até o tamanho do registrador) e overflow
- [[Níveis de Abstração]]
- [[Linguagem de Máquina]]
- [[Latch e Flip-Flop]] — flip-flop D é a célula básica de 1 bit de cada registrador
