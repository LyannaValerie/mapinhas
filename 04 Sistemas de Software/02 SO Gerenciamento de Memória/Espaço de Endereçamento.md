---
title: Espaço de Endereçamento
aliases:
  - address space
tags:
  - computação/sistemas
  - sistemas-operacionais/memória
source: "Sistemas Operacionais Modernos, cap. 3"
created_by: Codex
---

# Espaço de Endereçamento

Nota criada pelo Codex.

## Definição

Conjunto de endereços que um [[Processo]] pode usar para referenciar memória. A abstração separa endereços usados pelo programa dos endereços físicos presentes na [[Memória]].

## Papel técnico

Cada processo pode operar como se tivesse uma memória própria. O [[Sistema Operacional]] e a [[MMU]] traduzem endereços virtuais para endereços físicos, aplicam proteção e impedem que um processo leia ou escreva diretamente no espaço de outro.

## Funcionamento

Dois problemas precisam ser resolvidos para permitir múltiplos processos na memória:

**1. Proteção** — um processo não deve acessar memória de outro.

**2. Realocação** — o mesmo programa pode ser carregado em posições físicas diferentes; endereços no código precisam continuar válidos.

**Solução com registradores base e limite:**

- Ao despachar um processo, SO carrega: `REG_BASE = endereço físico de início` e `REG_LIMITE = tamanho do espaço`.
- Cada endereço gerado pela CPU (endereço virtual) é verificado: se `0 ≤ virtual < LIMITE`, MMU gera `físico = virtual + BASE`. Fora do intervalo → trap.
- Exemplos: endereço virtual `28` com `BASE = 16384` → acesso físico `16412`. Dois processos com bases diferentes nunca se sobrepõem.

**Limitação:** sem espaço de endereçamento maior que a RAM física. Resolvido pela [[Memória Virtual em Sistemas Operacionais]].

## Relações (SPO)

- Espaço de Endereçamento -> abstrai -> [[Memória]]
- Espaço de Endereçamento -> isola -> [[Processo|processos]]
- [[Memória Virtual em Sistemas Operacionais]] -> organiza -> Espaço de Endereçamento
- [[Tabela de Páginas]] -> mapeia -> endereços virtuais para quadros físicos

