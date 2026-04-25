---
title: Controlador de Teclado AT
aliases:
  - AT keyboard controller
  - controlador 8042
  - porta 60h
  - porta 64h
tags:
  - computação/arquitetura
  - computação/entrada
date: 2026-04-24
source: "PC System Programming — Abacus, cap. 12"
codex_note: criado pelo Codex
---

# Controlador de Teclado AT

## Definição

O controlador de teclado AT coordena comunicação entre teclado e sistema por protocolo serial síncrono. O teclado envia códigos de varredura quando teclas são pressionadas ou liberadas; esses códigos são independentes de ASCII e dos códigos estendidos produzidos depois pelo sistema.

## Comunicação

No AT, o controlador 8042 usa buffers e registrador de status:

- porta `60h`: dados do teclado e alguns bytes enviados ao teclado;
- porta `64h`: comandos e leitura do status do controlador;
- bit 0 do status: buffer de saída cheio;
- bit 1 do status: buffer de entrada cheio;
- bits adicionais sinalizam comando/dado, teclado ativo, timeout e erro de paridade.

Nada deve ser escrito no buffer de entrada enquanto o bit 1 indicar buffer cheio.

## Taxa typematic

O teclado AT permite configurar atraso e repetição de tecla. O comando de configuração é enviado ao teclado, seguido por um byte de parâmetros. A rotina precisa esperar o buffer ficar livre, enviar byte, ler resposta e validar `FAh` como acknowledge.

## Relações (SPO)

- [[Controlador de Teclado AT]] produz códigos de varredura para [[BIOS INT 16h]].
- [[Controlador de Teclado AT]] usa portas `60h` e `64h`.
- [[Teclado]] comunica eventos por make code e break code.
- [[BIOS INT 16h]] abstrai parte da complexidade do controlador.

## Ver também

- [[Teclado]]
- [[BIOS INT 16h]]
- [[Interrupção]]
- [[Serviços de BIOS do IBM PC]]
