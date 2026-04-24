---
title: Front End do Compilador
tags:
  - compiladores
  - programação
aliases:
  - compiler front end
  - front-end
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Compiladores]]

# Front End do Compilador

Responsável por entender o programa fonte e produzir [[Representação Intermediária (IR)|IR]] válida — ou reportar erros diagnósticos.

## Passes do front end

### 1. Scanner ([[Scanner — Análise Léxica]])
Converte sequência de caracteres em sequência de *tokens* classificados: pares `(categoria, lexema)`.

### 2. Parser ([[Parser — Análise Sintática]])
Verifica se a sequência de tokens satisfaz a gramática da linguagem. Produz árvore sintática.

### 3. Type Checker / Elaboração Semântica
Verifica consistência de tipos e número de argumentos.

Exemplos:
- `a ← a × 2 × b × c × d` — se `b` e `d` são strings, o programa é sintaticamente válido mas semanticamente inválido
- chamada de procedimento deve ter mesmo número de argumentos da definição

### 4. Geração de IR
Produz a [[Representação Intermediária (IR)]] definitiva que será passada ao otimizador/back end.

## Relação com outras fases

```
Scanner → Parser → Type Checker → Geração IR → [Otimizador] → [Back End]
```

Ver [[Compilador — Estrutura de Fases]].
