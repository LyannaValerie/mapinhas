---
title: BIOS Data e Hora
aliases:
  - BIOS INT 1Ah
  - relógio BIOS
  - realtime clock BIOS
tags:
  - computação/arquitetura
date: 2026-04-24
source: "PC System Programming — Abacus, cap. 7"
codex_note: criado pelo Codex
---

# BIOS Data e Hora

## Definição

`INT 1Ah` reúne serviços de [[BIOS do IBM PC]] para leitura e ajuste de relógio, data e contador de ticks.

## Funções (INT 1Ah)

| AH | Função | Disponibilidade | Observação |
|---|---|---|---|
| 00h | Ler contador de ticks | PC/XT/AT | CX=hi, DX=lo; AL≠0 → mais de 24h desde última leitura |
| 01h | Definir contador de ticks | PC/XT/AT | CX=hi, DX=lo |
| 02h | Ler relógio de tempo real | AT only | CH=horas, CL=minutos, DH=segundos (BCD); CF=1 → bateria morta |
| 03h | Definir relógio de tempo real | AT only | CH=horas, CL=minutos, DH=segundos, DL=1 se horário de verão |
| 04h | Ler data do RTC | AT only | CH=século, CL=ano, DH=mês, DL=dia (BCD) |
| 05h | Definir data do RTC | AT only | CH=século, CL=ano, DH=mês, DL=dia (BCD) |
| 06h | Definir alarme | AT only | CH=horas, CL=minutos, DH=segundos; dispara INT 4Ah |
| 07h | Cancelar alarme | AT only | Limpa alarme criado por fn 06h |

## Contador de ticks (fns 00h/01h)

Incrementa **18,2 vezes por segundo** (gerado pelo temporizador 8253/8254, canal 0). Ao atingir `01800B0h` (equivalente a 24 horas), é zerado e `AL` retorna valor não-zero na próxima leitura.

- **PC/XT**: contador inicia em 0 no boot (sem RTC com bateria)
- **AT**: contador é inicializado com a hora atual do RTC no boot

## Relógio de tempo real (fns 02h–07h)

Disponível apenas no AT e compatíveis — chip MC146818 (RTC) alimentado por bateria. Todos os valores são em **formato BCD** (Binary Coded Decimal).

> [!warning] Formato BCD
> Os registradores de hora/data retornam BCD, não binário. Ex: 23h59m → CH=`0x23`, CL=`0x59`, não `23` e `59` decimal.

## Alarme (fns 06h/07h)

Durante o boot, INT 4Ah aponta para uma instrução IRET — se não for redirecionada para rotina própria, o alarme dispara sem efeito visível. Somente um alarme ativo por vez; para alterar, cancelar com fn 07h antes de redefinir.

## Relógio

## Relações (SPO)

- [[BIOS Data e Hora]] pertence a [[Serviços de BIOS do IBM PC]].
- [[BIOS Data e Hora]] usa temporizador e relógio de tempo real.
- [[Firmware]] preserva parte da configuração via memória alimentada por bateria.

## Ver também

- [[BIOS do IBM PC]]
- [[Firmware]]
- [[Variáveis BIOS]]
- [[Serviços de BIOS do IBM PC]]
