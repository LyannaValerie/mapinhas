---
title: Arquivo Objeto
aliases:
  - object file
  - módulo objeto
  - arquivo relocável
tags:
  - computação/sistemas
source: "Linking and Loading"
created_by: Codex
---

# Arquivo Objeto

criado pelo Codex

## Definição

**Arquivo objeto** é a unidade intermediária emitida por assembler ou compilador. Contém código, dados, símbolos e informações de relocação suficientes para o [[Ligador]] montar um programa completo.

## Relações (SPO)

- Arquivo objeto → contém → seções de código e dados
- Arquivo objeto → expõe → [[Símbolos de Ligação]]
- Arquivo objeto → carrega → entradas de [[Relocação de Código]]
- [[Formato ELF]] → implementa → formato objeto em sistemas Unix modernos
- [[Biblioteca de Código Objeto]] → agrega → múltiplos arquivos objeto

## Conteúdo típico

- `.text`: instruções
- `.data`: dados inicializados
- `.bss`: dados zerados sem armazenamento explícito no arquivo
- tabela de símbolos
- tabela de strings
- registros de relocação
- metadados de depuração

## Ponto técnico

Arquivo objeto relocável não precisa conhecer o endereço final de execução. Ele preserva lacunas controladas para o ligador preencher depois.

