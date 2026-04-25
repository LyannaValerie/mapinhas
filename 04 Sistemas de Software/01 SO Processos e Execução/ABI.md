---
title: ABI
aliases:
  - Application Binary Interface
  - interface binária de aplicação
tags:
  - computação/sistemas
source: "Linking and Loading"
created_by: Codex
---

# ABI

criado pelo Codex

## Definição

**ABI** é o contrato binário que permite que código compilado, bibliotecas, sistema operacional e processador concordem sobre representação e chamada em nível de máquina.

## Relações (SPO)

- ABI → fixa → convenção de chamada
- ABI → define → layout de tipos, registradores e pilha
- ABI → orienta → [[Ligador]] e [[Carregador]]
- [[Formato ELF]] → materializa → parte do contrato binário
- [[Símbolos de Ligação]] → dependem → regras de nome e visibilidade

## Escopo

- formato de executável e objeto
- convenções de chamada
- uso de registradores
- alinhamento e tamanho de tipos
- representação de símbolos
- interface com chamadas de sistema

## Diferença para API

API é contrato em nível de código-fonte. ABI é contrato em nível binário. Quebrar ABI pode invalidar programas já compilados mesmo quando a API parece compatível.

