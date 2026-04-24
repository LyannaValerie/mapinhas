---
title: Escalonamento Global de Código
aliases:
  - global code scheduling
  - trace scheduling
  - superblock scheduling
  - compensation code
  - escalonamento global
tags:
  - computação/arquitetura
  - computação/ILP
date: 2026-04-22
source: "P&H Apêndice H §H.3"
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Escalonamento Global de Código

Técnica de escalonamento que move instruções **através de branches** (desvios), compactando código com fluxo de controle interno na menor sequência possível sem violar dependências de dados e controle.

## Relações (SPO)

| Sujeito | Predicado | Objeto |
|---|---|---|
| escalonamento global | move instruções através de | branches (desvios) |
| escalonamento local (§3.2) | funciona em | blocos básicos (sem desvios internos) |
| trace scheduling | usa | trilha de execução mais provável |
| superblock | é | sequência linear com uma entrada, várias saídas |
| compensation code | corrige | semântica ao longo de caminhos off-trace |
| [[Instruções Predicadas]] | elimina necessidade de | compensation code |

## Motivação

Escalonamento local (loop unrolling de §3.2) só funciona quando o corpo do loop é straight-line code. Quando há desvios internos, as instruções precisam ser movidas **através** dos branches — requer análise de:
1. Dependências de dados entre os blocos
2. Exceções: instrução movida antes de um branch que a guardava pode causar exceção indevida
3. Frequência relativa de cada caminho

## Desafios

Mover instrução de B para antes do branch `if`:

```
if (condição)
    B = ...;    // move para antes do if?
else
    ...
```

Para mover B acima do branch:
- **Se branch é taken (then):** B agora executa sempre — correto se dominado pelo then
- **Se branch é not taken (else):** B executou "a mais" → precisa **compensation code** no caminho else para desfazer efeito ou recalcular
- Depende de: frequência dos caminhos, custo da instrução, disponibilidade de registradores

## Trace Scheduling (Fisher, 1981)

1. **Escolhe trace:** caminho de execução mais provável através do CFG
2. **Escalona trace** como se fosse bloco básico gigante (desconsidera branches temporariamente)
3. **Adiciona compensation code** em todas as entradas e saídas do trace que o fluxo real pode tomar

**Desvantagens:**
- Compensation code pode crescer muito
- Múltiplos caminhos off-trace cada um precisa de correção
- Efetividade cai quando previsão de caminho é difícil

## Superblock Scheduling

Simplificação do trace scheduling: superblock é um **trace sem entradas no meio** (apenas tail duplication elimina joins).

```
Trace:   A → B → C → D
              ↑ join ← E   ← problema: entry no meio

Superblock: duplica código abaixo do join
   Caminho principal: A → B → C → D
   Caminho duplicado: E → B' → C' → D'  (B',C',D' são cópias)
```

Com uma única entrada, desvios só ocorrem **saindo** do superblock:
- Sem compensation code em joins
- Cada saída pode ter código de fixup se necessário
- Mais simples de implementar que trace scheduling completo

## Limitações Práticas

| Fator | Impacto |
|---|---|
| Previsão de caminho errada | Compensation code desperdiça ciclos |
| Code bloat por duplicação | Pressão em I-cache |
| Análise de aliasing de memória | Limita quais loads podem cruzar stores |
| Exceções | Load movido acima de branch que o guardava pode gerar fault indevido |

> [!note] Solução para exceções
> [[Instruções Predicadas]] eliminam o desvio completamente — a instrução executa só se o predicado for verdadeiro. Elimina a necessidade de compensation code para controle de fluxo.

## Ver também

- [[Software Pipelining]] — técnica complementar para loops sem desvios internos
- [[Instruções Predicadas]] — solução hardware para eliminar desvios e simplificar escalonamento global
- [[VLIW — Very Long Instruction Word]] — precisa de escalonamento global para preencher slots
- [[Especulação por Compilador]] — move loads especulativamente além de branches
- [[Paralelismo em Nível de Loop]] — análise de dependências de dados usada aqui
