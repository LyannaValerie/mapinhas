---
title: Forma de Código — Booleanos e Controle
tags:
  - compiladores
  - geração-de-código
  - code-shape
aliases:
  - boolean code shape
  - condition code
  - short-circuit evaluation
  - avaliação em curto-circuito
  - controle de fluxo
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Compiladores]]

# Forma de Código — Booleanos e Controle

Estratégias para implementar operadores booleanos/relacionais e construtos de controle de fluxo.

> [!info] Contexto
> Seções 7.4 e 7.8 de *Engineering a Compiler*. Parte do cap. 7 sobre [[Forma de Código — Expressões]].

## Representações de Valores Booleanos

### Codificação Numérica
Atribui valores concretos: `false = 0`, `true = 1` (ou qualquer não-zero).
- Resultado armazenado em registrador como inteiro
- `and`, `or`, `not` mapeiam para operações bitwise da arquitetura
- Necessário quando resultado é **armazenado em variável**

### Codificação Posicional
Codifica o valor como **posição no código**: dois rótulos representam *true* e *false*.
- Não produz valor concreto — usa branches para separar caminhos
- Mais eficiente quando resultado determina apenas **fluxo de controle**
- Base da **avaliação em curto-circuito**

## Avaliação em Curto-Circuito

Explora identidades booleanas:
```
false ∧ x = false   →  se operando esquerdo é false, ignorar direito
true  ∨ x = true    →  se operando esquerdo é true,  ignorar direito
```

Reduz operações quando ramos da expressão têm operadores caros ou efeitos colaterais.

> [!example] C exige curto-circuito
> `(x != 0 && y/x > 0.001)` — sem curto-circuito, `y/x` causa divisão por zero quando `x=0`

### Exemplo — `a<b ∨ c<d ∧ e<f`

Sem curto-circuito: 11 operações por caminho, 3 branches + 3 jumps.

Com codificação posicional + curto-circuito:
```iloc
comp ra, rb ⇒ cc1       // a < b
cbr LT cc1 → L3, L1    // se true → L3 (resultado true), else continua
L1: comp rc, rd ⇒ cc2
    cbr LT cc2 → L2, L4
L2: comp re, rf ⇒ cc3
    cbr LT cc3 → L3, L4
L3: loadI true ⇒ r5; jump → L5
L4: loadI false ⇒ r5
L5: nop
```

## Suporte de Hardware para Relacionais

### Condition Codes (CC)
- Operações aritméticas definem bits de CC (N, Z, C, V)
- Comparação explícita (`cmp`) também define CC
- Branch condicional (`cbr LT`, `cbr EQ`, etc.) consulta CC
- Vantagem: instruções aritméticas definem CC como efeito colateral → branch grátis

### Boolean-Valued Comparisons
- Operador de comparação retorna booleano em registrador
- Branch toma registrador como argumento
- Força quando resultado deve ser armazenado ou usado em `and`/`or` bitwise

### Condition Code Implícito em Aritmética
- `add r1, r2 → r3` define CC como efeito colateral
- Permite `cbr` logo após sem instrução `cmp` extra

## Construtos de Controle de Fluxo

### If-Then-Else
```iloc
// if (a < b) then S1 else S2
comp ra, rb ⇒ cc
cbr LT cc → L_then, L_else
L_then: [código de S1]; jump → L_end
L_else: [código de S2]
L_end:  nop
```

### Loops

**While**:
```
L_test: [avaliar condição] → L_body, L_exit
L_body: [corpo]; jump → L_test
L_exit: nop
```

**For** (com limites conhecidos em compile time):
- Compilador pode desenrolar (*loop unrolling*) se iterações são poucas e fixas

**Do-While**:
```
L_body: [corpo]
L_test: [avaliar condição] → L_body, L_exit
```
Mais eficiente: executa corpo antes do primeiro teste.

### Case/Switch

Três implementações possíveis:
| Estratégia | Quando usar |
|---|---|
| Cascata de if-else | poucos casos, valores esparsos |
| Jump table (tabela de rótulos) | muitos casos, valores densos |
| Hash table de rótulos | valores muito esparsos, muitos casos |

Jump table: `goto table[valor]` — custo O(1) independente do número de cases.

## Predicação

Alternativa a branches: instrução **predicada** executa somente se condição for verdadeira. Elimina branches, importante para pipelines profundos.

## Relações

- [[Forma de Código — Expressões]] — contexto geral do cap. 7
- [[IR Linear — Código de Três Endereços]] — IR alvo das sequências geradas
- [[IR Graphical — Árvores e Grafos]] — CFG modela o fluxo resultante
- [[Back End do Compilador]] — seleção de instrução e scheduling afetam código de controle
