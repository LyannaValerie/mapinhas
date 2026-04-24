---
title: VLIW — Very Long Instruction Word
aliases:
  - VLIW
  - Very Long Instruction Word
  - ILP estático
  - escalonamento estático de instruções
tags:
  - computação/arquitetura
  - computação/ILP
date: 2026-04-22
source: "P&H Apêndice H §H.1"
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# VLIW — Very Long Instruction Word

Abordagem de [[Paralelismo em Nível de Instrução]] onde o **compilador** detecta e expõe paralelismo estaticamente, empacotando múltiplas operações em uma única palavra de instrução larga.

## Relações (SPO)

| Sujeito | Predicado | Objeto |
|---|---|---|
| VLIW | é tipo de | [[Múltipla Emissão e Superescalar|múltipla emissão estática]] |
| VLIW | contrasta com | superescalar (múltipla emissão dinâmica) |
| compilador VLIW | detecta | paralelismo em tempo de compilação |
| hardware VLIW | não precisa de | lógica de detecção de dependências em runtime |
| VLIW | exige | compilador sofisticado |
| [[IA-64 e EPIC]] | é implementação de | modelo EPIC (evolução do VLIW) |

## Definição

Em VLIW, cada instrução contém **múltiplos campos de operação**, cada um destinado a uma unidade funcional específica (inteiro, FP, memória, desvio). O compilador preenche esses campos com operações independentes ou insere no-ops onde não há trabalho disponível.

```
[ op_int | op_fp | op_load | op_store | op_branch ]
   cada campo → unidade funcional distinta
```

## VLIW vs. Superescalar

| Aspecto | VLIW | Superescalar |
|---|---|---|
| Detecção de paralelismo | Compilador (compile time) | Hardware (runtime) |
| Lógica de dependências | No compilador | No hardware (Tomasulo, scoreboard) |
| Custo de hardware | Mais simples | Mais complexo |
| Desempenho sem bom compilador | Ruim | Razoável |
| Compatibilidade binária | Frágil (layout fixo de slots) | Preservada |
| Slots vazios | Visíveis (no-ops explícitos) | Ocultos pelo hardware |

## Limitações do VLIW Puro

- **Code bloat:** no-ops explícitos inflam tamanho do código
- **Incompatibilidade binária:** binários não portáveis entre versões do processador (número/tipo de unidades pode mudar)
- **Informação em runtime ausente:** compilador não conhece padrões de cache miss, branch outcomes reais
- **Dependências de memória:** compilador não sabe se dois endereços de memória são distintos sem análise complexa

## EPIC — Evolução do VLIW

O modelo **EPIC** (Explicitly Parallel Instruction Computing), usado no [[IA-64 e EPIC|IA-64]], refinа o VLIW:
- Instruções empacotadas em **bundles de 128 bits** (3 syllables × 41 bits + 5-bit template)
- Template informa unidades funcionais e fronteiras de grupo (stops)
- **Grupo de instruções:** conjunto sem dependências RAW/WAW — podem executar em qualquer ordem
- Stop (`;;`) indica fim de grupo; instruções sem stop entre elas = mesmo grupo
- Hardware NÃO precisa verificar dependências dentro do grupo — responsabilidade do compilador

## Técnicas de Compilação para VLIW

- [[Paralelismo em Nível de Loop]] — detectar quais loops são paralelizáveis
- [[Software Pipelining]] — overlap entre iterações de loop
- [[Escalonamento Global de Código]] — mover instruções através de desvios
- [[Instruções Predicadas]] — eliminar desvios com execução condicional
- [[Especulação por Compilador]] — mover loads acima de stores potencialmente conflitantes

## Ver também

- [[IA-64 e EPIC]] — implementação EPIC/VLIW da Intel/HP
- [[Múltipla Emissão e Superescalar]] — abordagem dinâmica equivalente
- [[Paralelismo em Nível de Instrução]] — conceito base
- [[Escalonamento Dinâmico]] — alternativa em hardware (Tomasulo)
- [[Software Pipelining]] — técnica central para VLIW em loops
