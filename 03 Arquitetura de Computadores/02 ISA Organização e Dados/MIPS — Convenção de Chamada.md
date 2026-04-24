---
title: MIPS — Convenção de Chamada
tags:
  - arquitetura/MIPS
aliases:
  - calling convention MIPS
  - procedure call MIPS
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# MIPS — Convenção de Chamada

## Protocolo Básico

```
Caller:
  1. coloca argumentos em $a0–$a3
  2. jal ProcedureName        # $ra = PC+4; PC = Procedure

Callee:
  3. executa
  4. coloca resultado em $v0–$v1
  5. jr $ra                   # retorna ao caller
```

`jal` = jump-and-link: único passo que salva o endereço de retorno.

## Registradores por Responsabilidade

| Categoria | Registradores | Quem salva |
|---|---|---|
| Argumentos | `$a0–$a3` | caller (se precisar após call) |
| Retorno | `$v0–$v1` | — |
| Temporários | `$t0–$t9` | caller |
| Salvos | `$s0–$s7` | **callee** |
| Stack pointer | `$sp` | callee (restaura exato) |
| Frame pointer | `$fp` | callee |
| Return address | `$ra` | callee (se non-leaf) |

## Leaf vs Non-leaf

**Leaf procedure** — não chama outras funções:
- Não precisa salvar `$ra`
- Pode usar só `$t0–$t9` sem spill

**Non-leaf procedure** — chama outras funções:
- Deve salvar `$ra` antes de usar `jal`
- Deve salvar qualquer `$s` que for usar

## Stack Frame

Stack cresce para baixo (endereço decresce). `$sp` aponta para o topo (último item válido).

```
alta  │ frame do caller    │
      ├────────────────────┤ ← $sp do caller no momento da call
      │ $ra (se non-leaf)  │
      │ $fp (se necessário)│
      │ $s0–$s7 (usados)   │
      │ variáveis locais   │
      │ args extras (>4)   │
      ├────────────────────┤ ← $sp do callee
baixa │        ...         │
```

Alocação/liberação:
```mips
addi $sp,$sp,-12    # aloca 3 words
sw   $s0,8($sp)
sw   $t0,4($sp)
sw   $t1,0($sp)
# ... corpo ...
lw   $t1,0($sp)
lw   $t0,4($sp)
lw   $s0,8($sp)
addi $sp,$sp,12     # libera
jr   $ra
```

## Exemplo: Procedimento Recursivo (fat(n))

```mips
fact:
    addi $sp,$sp,-8
    sw   $ra,4($sp)      # salva $ra
    sw   $a0,0($sp)      # salva n
    slti $t0,$a0,1
    beq  $t0,$zero,L1
    addi $v0,$zero,1     # caso base: retorna 1
    addi $sp,$sp,8
    jr   $ra
L1: addi $a0,$a0,-1
    jal  fact            # fact(n-1) — sobrescreve $ra!
    lw   $a0,0($sp)      # restaura n
    lw   $ra,4($sp)      # restaura $ra
    addi $sp,$sp,8
    mul  $v0,$a0,$v0     # n * fact(n-1)
    jr   $ra
```

## Variáveis Estáticas vs Automáticas

| Tipo | Armazenamento | Lifetime |
|---|---|---|
| Automatic | Stack (`$sp`) | duração da função |
| Static | Segmento de dados (`$gp`) | duração do programa |

`$gp` (global pointer) aponta para a área de dados estáticos — acesso eficiente com offset de 16 bits.

## Preservado vs Não Preservado

| Preservado | Não preservado |
|---|---|
| `$s0–$s7` | `$t0–$t9` |
| `$sp` | `$a0–$a3` |
| `$fp` | `$v0–$v1` |
| `$ra` | — |

## Ver também

- [[MIPS — ISA]]
- [[MIPS — Registradores]]
- [[Pilha]]
- [[Convenção de Chamada x86-64]] — comparativo
- [[Salto Não-local]]
