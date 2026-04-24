---
title: Caminho de Dados — MIPS
tags:
  - arquitetura
  - mips
  - processador
  - datapath
aliases:
  - MIPS Datapath
  - Datapath MIPS
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Caminho de Dados — MIPS

Conjunto de componentes funcionais que executam instruções MIPS. Construído incrementalmente por classe de instrução (P&H Cap. 5).

> [!note] Dois tipos de elementos lógicos
> - **Combinatório**: saída depende só das entradas atuais (ex.: [[ULA]], MUX, somador). Sem estado.
> - **Sequencial (estado)**: contém armazenamento interno; saída depende de entradas e estado anterior (ex.: registradores, memórias, PC). Escritas controladas por clock.

## Metodologia de Clock

**Edge-triggered**: toda escrita em elemento de estado ocorre na borda do clock. Leituras podem ocorrer a qualquer momento.

- Evita condição de corrida: leitura e escrita do mesmo registrador no mesmo ciclo é segura — leitura vê valor do ciclo anterior.
- **Sinal asserted** = logicamente alto; **deasserted** = logicamente baixo.

## Componentes do Datapath

### Busca de Instrução

| Componente | Função |
|---|---|
| **Instruction Memory** | ROM; lê instrução no endereço dado pelo PC |
| **PC** | Registrador 32 bits; atualizado a cada ciclo |
| **Somador PC+4** | ALU fixada em ADD; computa endereço da próxima instrução |

```
PC → Instruction Memory → instrução de 32 bits
PC → Add(+4) → PC+4 (próxima instrução)
```

### Instruções R-type (add, sub, and, or, slt)

| Componente | Função |
|---|---|
| **Register File** | 32 registradores; 2 portas de leitura, 1 de escrita |
| **[[ULA]]** | 4 bits de controle; produz resultado 32 bits + flag Zero |

- Register File: entradas = 3 números de registrador (5 bits cada) + dado a escrever (32 bits); saídas = 2 dados lidos (32 bits cada).
- Escrita controlada por **RegWrite** (edge-triggered).

### Load/Store (lw, sw)

| Componente | Função |
|---|---|
| **Sign-extend** | Expande offset[15:0] para 32 bits com extensão de sinal |
| **Data Memory** | Memória de dados; sinais **MemRead** e **MemWrite** |

Endereço = Reg[rs] + sign_extend(offset).

### Branch (beq)

- Comparação: ULA subtrai os dois registradores; flag **Zero** indica igualdade.
- Branch target: PC+4 + (sign_extend(offset) << 2).
- MUX seleciona entre PC+4 e branch target com base em Zero e PCSrc.

> [!info] Delayed branch
> MIPS real usa delayed branch (instrução após branch sempre executa). Por simplificação, P&H Cap. 5 implementa **nondelayed beq**.

### Instrução Jump (j)

PC[31:28] concatenado com IR[25:0] << 2 (substitui 28 bits baixos do PC).

## Datapath Unificado (Monociclo)

Combina todos os componentes com MUXes:

| Sinal MUX | Seleciona |
|---|---|
| **RegDst** | rd (R-type) vs rt (load) como registrador destino |
| **ALUSrc** | Reg[rt] vs sign_extend(imm) como 2ª entrada da ULA |
| **MemtoReg** | Saída da ULA vs dado da memória para write-back |
| **PCSrc** | PC+4 vs branch target |

## Relações SPO

| Sujeito | Predicado | Objeto |
|---|---|---|
| Register File | tem | 2 portas leitura + 1 escrita |
| ULA | produz | resultado 32b + flag Zero |
| Sign-extend | expande | offset 16b → 32b |
| beq | usa ULA para | comparação por subtração |
| branch target | calculado como | PC+4 + (offset<<2) |
| jump | substitui | bits [27:0] do PC |

## Ver também

- [[Implementação Monociclo]] — controle do datapath em ciclo único
- [[Implementação Multiciclo]] — datapath compartilhado em múltiplos ciclos
- [[ULA]] — unidade lógico-aritmética
- [[MIPS — Formato de Instrução]] — campos rs, rt, rd, imm, offset
- [[MIPS — Conjunto de Instruções]] — instruções implementadas
- [[Caminho de Dados]] — datapath do Mic-1 (Tanenbaum), contexto diferente
