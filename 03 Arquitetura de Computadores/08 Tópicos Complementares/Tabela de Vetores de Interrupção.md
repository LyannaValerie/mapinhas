---
title: Tabela de Vetores de Interrupção
aliases:
  - IVT
  - Interrupt Vector Table
  - tabela de interrupções do PC
  - vetor de interrupção 8088
tags:
  - computação/arquitetura
date: 2026-04-24
created_by: Codex
source: desconhecida
---

# Tabela de Vetores de Interrupção

Nota criada pelo Codex.

## Definição

A tabela de vetores de interrupção é a estrutura usada no PC real-mode para mapear um número de interrupção para o endereço da rotina de atendimento correspondente.

No [[Processador 8088]], cada vetor aponta para um par **segmento:deslocamento**. O vetor permite que uma interrupção de [[Hardware]], uma interrupção de software ou uma exceção transfira controle para código de tratamento sem codificar o endereço da rotina dentro do programa interrompido.

## Relações (SPO)
- Interrupção → indexa → tabela de vetores
- Vetor de interrupção → identifica → rotina de atendimento
- Rotina de atendimento → salva e restaura → estado do programa interrompido
- PC em modo real → representa endereço → segmento:deslocamento
- [[BIOS do IBM PC]] → expõe serviços por → interrupções
- [[MS-DOS]] → expõe serviços por → `INT 21h`
- [[Programa TSR DOS]] → altera → tabela de vetores de interrupção

---

## Funcionamento

Quando uma interrupção ocorre:

1. a execução corrente é suspensa;
2. parte do estado do processador é preservada;
3. o número da interrupção é usado como índice na tabela;
4. o processador carrega o endereço da rotina de atendimento;
5. a rotina trata o evento;
6. uma instrução de retorno de interrupção devolve o controle ao ponto interrompido.

Em sistemas x86 posteriores, a [[Interrupção#Funcionamento: IDT|IDT]] generaliza o mesmo papel com descritores protegidos.

## Ver também
- [[Interrupção]]
- [[Processador 8088]]
- [[BIOS do IBM PC]]
- [[MS-DOS]]
- [[Chamada do Sistema]]
- [[Programa TSR DOS]]
