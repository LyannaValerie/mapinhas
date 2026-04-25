---
title: Carregador
aliases:
  - loader
  - carregador de programa
tags:
  - computação/sistemas
source: "Linking and Loading"
created_by: Codex
---

# Carregador

criado pelo Codex

## Definição

**Carregador** é o componente do sistema que coloca uma imagem executável no espaço de endereços do processo e inicia sua execução.

## Relações (SPO)

- Carregador → consome → executável produzido pelo [[Ligador]]
- Carregador → cria → mapeamentos de código, dados, heap e pilha
- Carregador → aplica → [[Relocação de Código]] pendente
- Carregador → invoca → [[Ligação Dinâmica]] quando há dependências compartilhadas
- [[Formato ELF]] → descreve → segmentos carregáveis e ponto de entrada

## Funções

- validar formato do executável
- mapear segmentos em memória virtual
- inicializar dados, BSS e permissões de página
- preparar argumentos, ambiente e pilha inicial
- transferir controle ao entry point

## Limite conceitual

O ligador decide layout e resolve nomes no arquivo. O carregador materializa esse layout em memória no contexto de um processo real.

