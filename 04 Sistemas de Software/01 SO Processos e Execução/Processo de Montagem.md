---
title: Processo de Montagem
aliases:
  - assembler de duas passagens
  - duas passagens
  - passagem um
  - passagem dois
  - tabela de símbolos
  - ILC
  - instruction location counter
tags:
  - computação/arquitetura
date: 2026-04-13
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Sistemas de Software]]

# Processo de Montagem

## Definição
Processo pelo qual um [[Linguagem Assembly|assembler]] converte um programa em linguagem assembly para código objeto (linguagem de máquina). Opera em **duas passagens** para resolver o problema da referência antecipada.

## Relações (SPO)
- Assembler → opera em → duas passagens
- Passagem 1 → constrói → tabela de símbolos
- Passagem 2 → gera → código objeto
- ILC → rastreia → endereço da instrução corrente durante montagem
- Referência antecipada → motiva → abordagem de duas passagens

## Problema da referência antecipada

Um desvio para o rótulo `L` no início do programa não pode ser resolvido em uma única leitura se `L` está definido mais adiante. O assembler não conhece o endereço de `L` até ler quase o programa inteiro.

**Soluções**:
1. Ler o programa-fonte duas vezes (passagem 1 coleta símbolos, passagem 2 gera código)
2. Converter para forma intermediária na memória e fazer segunda passagem sobre ela (economiza E/S)

## Passagem 1

**Objetivo**: construir a **tabela de símbolos**.

Tarefas:
- Salvar definições de macros e expandir chamadas à medida que aparecem
- Rastrear o **ILC** (Instruction Location Counter) — começa em 0, incrementado pelo comprimento de cada instrução processada
- Para cada linha: extrair rótulo (se houver) → inserir na tabela de símbolos com valor = ILC atual
- Detectar literais e acumulá-las na tabela de literais
- Escrever arquivo temporário com tipo, opcode, comprimento e linha original (para poupar análise na passagem 2)
- Ao encontrar `END`: ordenar tabela de literais, remover duplicatas, rebobinar arquivo temporário

### ILC em ação

```
Rótulo      Opcode  Operandos        Comprimento  ILC
MARIA:      MOV     EAX, I           5            100
            MOV     EBX, J           6            105
            MOV     ECX, K           6            111
            IMUL    EAX, EAX         2            117
...
DONE:       ...                      ...          129
```

### Tabela de símbolos

Memória associativa: dado o símbolo, retorna seu valor numérico.

| Símbolo | Valor |
|---|---|
| MARIA | 100 |
| ROBERTA | 111 |
| MARILYN | 125 |
| STEPHANY | 129 |

Cada entrada pode conter também: comprimento do campo de dados associado, bits de relocação, visibilidade (local/exportado).

**Organização da tabela — três métodos**:

| Método | Busca | Inserção | Observação |
|---|---|---|---|
| Array linear | O(n) — linear | O(1) | Simples, lento |
| Array ordenado + busca binária | O(log n) | O(n) | Mais rápido, exige manter ordem |
| Hash (codificação hash) | O(1) médio | O(1) médio | Melhor se símbolos não são deletados |

**Hashing**: função hash mapeia símbolo → índice 0..k-1. Colisões tratadas com lista encadeada. Com k ≈ n, busca média em ~1 comparação.

### Tabela de opcodes

Contém para cada mnemônico: opcode simbólico, tipos de operandos esperados, valor numérico do opcode, comprimento da instrução, classe (tipo de processamento).

## Passagem 2

**Objetivo**: gerar o programa-objeto e (opcionalmente) a listagem de montagem.

Tarefas:
- Ler arquivo temporário linha por linha
- Para cada instrução: chamar procedimento de geração de código correspondente à sua classe
- Resolver referências a símbolos usando a tabela de símbolos já completa
- Escrever código binário (acumulado em buffer para minimizar E/S)
- Produzir listagem (código hex + linha fonte)
- Fornecer ao [[Ligador]] informações de relocação e referências externas

## Erros comuns detectados pelo assembler

1. Símbolo usado mas não definido
2. Símbolo definido mais de uma vez
3. Opcode inválido no campo de opcode
4. Número insuficiente ou excessivo de operandos
5. Número com caractere inválido (ex: `143G6`)
6. Uso ilegal de registrador
7. Pseudoinstrução `END` ausente

## Literais

Constantes para as quais o assembler reserva memória automaticamente, sem exigir declaração explícita. Exemplo IBM 360:
```asm
L 14, =F'5'    ; carrega registrador 14 com constante inteira 5
```

O assembler aloca memória para `5` no final do programa e gera instrução que a referencia. Tabela de literais é construída na passagem 1, ordenada e deduplicada ao final. Instruções imediatas modernas substituem essa necessidade.

## Ver também
- [[Linguagem Assembly]]
- [[Macros Assembly]]
- [[Ligador]]
- [[Tradução e Interpretação]]
- [[ISA]]
