---
title: Bibliotecas de Código
aliases:
  - code libraries
  - biblioteca de funções
tags:
  - computação/fundamentos
source: Programming from the Ground Up (pgubook), metadados bibliográficos completos ausentes no texto local
created_by: Codex
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Programação]]

# Bibliotecas de Código

## Definição

Biblioteca de código é coleção reutilizável de funções ou rotinas compiladas para serem usadas por vários programas sem duplicar implementação em cada projeto.

## Relações (SPO)
- Biblioteca → agrupa → funções reutilizáveis
- Programa → liga contra → biblioteca
- Ligador → resolve → símbolos externos
- Biblioteca compartilhada → reduz → duplicação em memória e disco
- Interface estável → permite → troca de implementação

## Papel estrutural

Bibliotecas separam contrato e implementação. O programa chamador depende dos nomes, assinaturas e comportamento prometido; a implementação pode evoluir se preservar esse contrato.

## Ver também

- [[Biblioteca Padrão C]]
- [[Funções em C]]
- [[Ligador]]
- [[PIC e GOT]]

## Nota de integração

criado pelo Codex
