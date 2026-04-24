---
title: ILOC — Linguagem Intermediária
tags:
  - compiladores
  - ir
  - assembly
  - ec/apêndiceA
aliases:
  - ILOC
  - iloc
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Compiladores]]

# ILOC — Linguagem Intermediária

Assembly para máquina abstrata RISC simples; IR linear usada como exemplo em *Engineering a Compiler*.  
Originalmente desenvolvida no Massively Scalar Compiler Project (Rice University).

Usado no livro como: IR de baixo nível para exemplos de otimização e como linguagem alvo simplificada nos capítulos de geração de código.

## Máquina Abstrata

- Registradores: ilimitados (`r0`, `r1`, ... ou nomes simbólicos como `ri`)
- `rarp`: reservado como ponteiro para o activation record atual
- Operações: três endereços, register-to-register
- Resultado definido ao **final** do ciclo em que a op completa
- Fonte disponível no **início** do ciclo em que a op emite

## Formato de Programa

```
label: opcode src1, src2, ... ⇒ dst1, dst2, ...
```

Instrução multi-operação (emitida no mesmo ciclo):
```
[ op1; op2; op3 ]
```

Comentários: `//` até fim de linha.

## Modos de Endereçamento

| Modo | Operação | Semântica |
|------|----------|-----------|
| Direto | `load r1 ⇒ r2` | `r2 ← MEM[r1]` |
| Offset imediato | `loadAI r1, @x ⇒ r2` | `r2 ← MEM[r1 + @x]` |
| Offset em registrador | `loadAO r1, r2 ⇒ r3` | `r3 ← MEM[r1 + r2]` |
| Imediato | `loadI 42 ⇒ r1` | `r1 ← 42` |

`@x` = offset simbólico da variável `x` no activation record.

## Operações Aritméticas

```
add    r1, r2 ⇒ r3      // r3 ← r1 + r2
sub    r1, r2 ⇒ r3
mult   r1, r2 ⇒ r3
div    r1, r2 ⇒ r3
addI   r1, c  ⇒ r3      // c = constante imediata
subI   r1, c  ⇒ r3
multI  r1, c  ⇒ r3
```

## Shifts

```
lshift  r1, r2 ⇒ r3
rshift  r1, r2 ⇒ r3
lshiftI r1, c  ⇒ r3
rshiftI r1, c  ⇒ r3
```

## Operações de Memória

```
load    r1         ⇒ r2       // r2 ← MEM[r1]
store   r1         → r2       // MEM[r2] ← r1
loadAI  r1, @x     ⇒ r2       // r2 ← MEM[r1 + @x]
storeAI r1         → r2, @x   // MEM[r2 + @x] ← r1
loadAO  r1, r2     ⇒ r3       // r3 ← MEM[r1 + r2]
storeAO r1         → r2, r3   // MEM[r2 + r3] ← r1
loadI   c          ⇒ r1       // r1 ← c  (literal)
```

Versões `c` (ex: `cloadAI`): load condicional (algumas extensões).

## Cópias Register-to-Register

```
i2i r1 ⇒ r2    // inteiro para inteiro
c2c r1 ⇒ r2    // (variantes de classe)
```

## Controle de Fluxo

Comparação seguida de branch condicional:
```
cmp_LT r1, r2 ⇒ r3    // r3 ← (r1 < r2)
cbr    r3 → L1, L2     // se r3 então goto L1 senão goto L2
```

Comparações: `cmp_LT`, `cmp_LE`, `cmp_EQ`, `cmp_NE`, `cmp_GT`, `cmp_GE`.

Jumps:
```
jumpI  → L1        // goto L1 (incondicional, alvo imediato)
jump   r1 → L1     // goto r1 (alvo em registrador; L1 = possível alvo para análise de CFG)
```

Sintaxe alternativa (caps. anteriores ao Apêndice A):
```
if r1 < r2 then goto L1 else goto L2
```

`cbr` / `jumpI` facilitam construção do CFG por ter alvos explícitos na instrução.

## Forma SSA em ILOC

$\phi$-funções com múltiplas fontes e alvo único:
```
r3 ← φ(r1, r2)    // r3 recebe r1 ou r2 dependendo do predecessor
```

## Relações

- [[IR Linear — Código de Três Endereços]] — ILOC é instância de IR linear de três endereços
- [[Forma SSA]] — ILOC suporta representação SSA via φ-funções
- [[Escalonamento de Instruções]] — ILOC como formato de entrada do escalonador
- [[Alocação de Registradores]] — ILOC como formato de entrada/saída do alocador
- [[Back End do Compilador]] — ILOC como IR de back end
