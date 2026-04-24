---
title: MIPS — Registradores
tags:
  - arquitetura/MIPS
aliases:
  - registradores MIPS
  - MIPS registers
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# MIPS — Registradores

MIPS tem **32 registradores de 32 bits**. Operações aritméticas só operam em registradores — dados devem ser carregados da memória primeiro.

## Tabela de Convenções

| Nome | Número | Uso | Preservado pelo callee? |
|---|---|---|---|
| `$zero` | 0 | Constante 0 (hardwired) | — |
| `$at` | 1 | Reservado para o assembler | — |
| `$v0–$v1` | 2–3 | Retorno de função | Não |
| `$a0–$a3` | 4–7 | Argumentos de função | Não |
| `$t0–$t7` | 8–15 | Temporários (caller-saved) | Não |
| `$s0–$s7` | 16–23 | Salvos (callee-saved) | **Sim** |
| `$t8–$t9` | 24–25 | Temporários adicionais | Não |
| `$k0–$k1` | 26–27 | Reservado para SO/exceções | — |
| `$gp` | 28 | Global pointer (dados estáticos) | **Sim** |
| `$sp` | 29 | Stack pointer | **Sim** |
| `$fp` | 30 | Frame pointer | **Sim** |
| `$ra` | 31 | Return address (escrito por `jal`) | **Sim** |

## Regra Caller/Callee

```
Caller-saved ($t0–$t9): caller salva SE precisar após a chamada
Callee-saved ($s0–$s7): callee DEVE salvar/restaurar SE usar
```

> [!tip] Redução de spilling
> Separação $t/$s permite que o callee não salve registradores que o caller não usa — reduz stores/loads desnecessários.

## $zero

`$zero` sempre retorna 0; escritas são ignoradas. Usado para criar pseudoinstruções:
```mips
move $t0, $t1   # pseudoinstrução: add $t0, $zero, $t1
```

## Propriedades de Memória

- Memória byte-addressed: $2^{32}$ bytes = $2^{30}$ words
- Stack cresce de endereços altos → baixos
- `$sp` aponta para o topo da pilha (último elemento válido)

## Ver também

- [[MIPS — ISA]]
- [[MIPS — Convenção de Chamada]]
- [[Registrador]]
- [[Convenção de Chamada x86-64]] — comparativo com x86-64
