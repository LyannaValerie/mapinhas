---
title: Símbolos de Ligação
aliases:
  - linker symbols
  - símbolos externos
  - resolução de símbolos
tags:
  - computação/sistemas
source: "Linking and Loading"
created_by: Codex
---

# Símbolos de Ligação

criado pelo Codex

## Definição

**Símbolos de ligação** são nomes usados para conectar referências e definições entre arquivos objeto, bibliotecas e unidades de compilação.

## Relações (SPO)

- [[Arquivo Objeto]] → declara → símbolos definidos e indefinidos
- [[Ligador]] → resolve → referência externa contra definição disponível
- [[Biblioteca de Código Objeto]] → fornece → definições sob demanda
- [[Ligação Dinâmica]] → preserva → parte dos símbolos para resolução posterior
- name mangling → codifica → tipo, namespace ou assinatura no nome simbólico

## Classes comuns

- **definido**: símbolo com endereço ou valor no módulo.
- **indefinido**: referência que precisa ser satisfeita externamente.
- **local**: visível só dentro do módulo.
- **global**: candidato a resolução por outros módulos.
- **weak**: definição fraca, substituível por definição forte.

## Observação

Tabela de símbolos de compilador organiza nomes durante análise semântica. Símbolos de ligação já operam no nível binário e intermodular.

