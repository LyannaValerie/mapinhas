---
title: Relógio de Tempo Real AT
aliases:
  - AT realtime clock
  - RTC AT
  - Motorola MC146818
tags:
  - computação/arquitetura
  - computação/baixo-nível
date: 2026-04-24
source: "PC System Programming — Abacus, cap. 11"
codex_note: criado pelo Codex
---

# Relógio de Tempo Real AT

## Definição

O relógio de tempo real do IBM AT mantém data, hora e parte da configuração do sistema em RAM alimentada por bateria. No PC AT, ele é associado ao Motorola MC146818 e expõe registradores acessíveis por portas de E/S.

## Acesso por portas

- Porta `70h`: seleciona o registrador interno.
- Porta `71h`: lê ou escreve o valor do registrador selecionado.

Registradores centrais:

- `0`: segundo atual
- `1`: segundo do alarme
- `2`: minuto atual
- `3`: minuto do alarme
- `4`: hora atual
- `5`: hora do alarme
- `6`: dia da semana
- `7`: dia do mês
- `8`: mês
- `9`: ano
- `10` a `13`: registradores de status `A` a `D`

## Registradores de status

O registrador `A` controla frequência de interrupção e indica se a atualização do tempo está em andamento. Leitura consistente dos campos de hora deve evitar o intervalo em que o relógio atualiza segundos, minutos e horas.

O registrador `B` controla horário de verão, modo 12/24 horas, formato BCD/binário, interrupção após atualização, alarme, interrupção periódica e bloqueio temporário da atualização.

## Relações (SPO)

- [[Relógio de Tempo Real AT]] complementa [[BIOS Data e Hora]].
- [[Relógio de Tempo Real AT]] usa portas `70h` e `71h`.
- [[BIOS Data e Hora]] assume representação BCD em chamadas de data.
- [[Relógio de Tempo Real AT]] preserva estado via bateria.

## Ver também

- [[BIOS Data e Hora]]
- [[BIOS Configuração do Sistema]]
- [[Variáveis BIOS]]
- [[Interrupção]]
