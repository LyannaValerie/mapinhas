---
title: Saltos Não-locais em C
aliases:
  - setjmp em C
  - longjmp em C
  - jmp_buf
  - salto não-local C
tags:
  - computação/programação/c
date: 2026-04-24
source: desconhecida
created_by: "criado pelo Codex"
---

# Saltos Não-locais em C

Saltos não-locais em C usam `setjmp` e `longjmp` para transferir controle para um ponto salvo anteriormente. O mecanismo quebra disciplina normal de chamada e retorno, por isso deve ficar restrito a condições excepcionais.

## Relações (SPO)

- `setjmp` — registra — ponto de retorno em `jmp_buf`
- `longjmp` — transfere controle para — ponto registrado por `setjmp`
- [[Fluxo de Controle Excepcional]] — inclui — salto não-local em nível de programa
- [[Sinais em C]] — pode combinar com — saída excepcional controlada, com fortes restrições

## Semântica operacional

Na passagem normal, `setjmp` marca o alvo e retorna `0`. Após `longjmp`, a execução reaparece no mesmo `setjmp`, mas com valor diferente de `0`. `longjmp` não retorna para seu chamador.

## Restrições críticas

- Objetos automáticos modificados entre `setjmp` e `longjmp` têm valor indeterminado se não forem `volatile`.
- `jmp_buf` costuma esconder tipo de array; passe e armazene com cuidado.
- Use quando retorno comum ficaria caro ou distorceria a interface, não como fluxo normal.

## Ver também

- [[Saltos Curtos em C]]
- [[Fluxo de Controle Excepcional]]
- [[Sinais em C]]
- [[Qualificadores de Tipo em C]]
