---
title: Especialização de Chamadas — Tail Call e Promoção
tags:
  - compiladores
  - otimização
  - especialização
  - chamadas
aliases:
  - tail call optimization
  - leaf call optimization
  - parameter promotion
  - otimização de tail call
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Compiladores]]

# Especialização de Chamadas — Tail Call e Promoção

Técnicas que especializam o código de linkagem de procedimento ao contexto de chamada. Fonte: Engineering a Compiler, Cap. 10.4.

---

## 10.4.1 Tail-Call Optimization

**Tail call**: última ação de um procedimento é uma chamada.

Quando `p` chama `q` como tail call, nenhuma computação útil ocorre entre o retorno de `q` e o epílogo de `p`. Preservar/restaurar o estado de `p` para essa chamada é inútil.

### Otimização

| Elemento | Comportamento normal | Tail call otimizado |
|---------|---------------------|---------------------|
| Caller-saves registers | Salvar antes da chamada | Não salvar (não podem estar vivos) |
| Activation record | Alocar novo AR para q | q reutiliza o AR de p |
| Return address/ARP | Preservados para retorno a o | Mantidos intactos (retorno vai direto a o) |
| Prologue de q | Padrão | Prologue customizado: pula setup já feito |

**Resultado**: tail call tem custo ≈ jump. Tail recursão → equivalente a iteração.

> [!note]
> Implementação exige precall sequence customizado em `p` e prologue adaptado em `q`. Uma vez otimizado, o código de postreturn em `p` torna-se inatingível e pode ser removido.

---

## 10.4.2 Leaf-Call Optimization

Procedimento **folha** não faz chamadas.

- Não precisa salvar return address no AR (não haverá chamada subsequente)
- Não precisa atualizar display para variáveis não-locais
- Preferir caller-saves registers (evitar save/restore de callee-saves)
- Se caller-saves forem insuficientes: spill pelo alocador de registradores

---

## 10.4.3 Promoção de Parâmetros

### Problema

Parâmetro passado por referência pode ser **ambíguo** (aliased):
- Mesmo parâmetro em duas posições distintas
- Variável global passada como parâmetro

Sem análise interprocedural: compilador conservador — cada uso = load, cada def = store.

### Solução: Promoção

Se o compilador prova que o parâmetro é **não-ambíguo** no callee:
1. Promover valor para escalar local → manter em registrador
2. Se callee não modifica: passar por valor
3. Se callee modifica e resultado está vivo no caller: semântica **value-result**

Para aplicar a `p`:
- Identificar todos os call sites que invocam `p`
- Provar que a promoção é válida em todos, **ou**
- Clonar `p` para criar versão que assume parâmetro não-ambíguo

> [!tip]
> Técnica mais atraente em linguagens com call-by-reference (Fortran, C com ponteiros). Frequentemente requer [[Análise Sensível ao Contexto]] ou clonagem.

---

## Relações

- [[Otimizador de Compilador]] — contexto de passes de otimização
- [[Abstração de Procedimento]] — linkage, AR, calling conventions
- [[Passagem de Parâmetros]] — call-by-reference, value-result
- [[Análise Sensível ao Contexto]] — interprocedural analysis para provar não-ambiguidade
- [[Transformações Habilitadoras — Cloning e Unswitching]] — procedure cloning como alternativa à promoção
