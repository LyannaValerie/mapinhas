---
title: Interface de Programa Linux x86
aliases:
  - ABI Linux x86 didática
  - interface Linux x86 assembly
tags:
  - computação/fundamentos
source: Programming from the Ground Up (pgubook), metadados bibliográficos completos ausentes no texto local
created_by: Codex
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Sistemas de Software]]

# Interface de Programa Linux x86

## Definição

Interface de programa Linux x86 é o contrato prático entre processo em espaço de usuário e sistema operacional: argumentos iniciais, pilha, descritores de arquivo, códigos de chamada de sistema e registradores usados para passagem de parâmetros.

## Relações (SPO)
- Processo Linux → inicia com → argumentos na pilha
- Argumentos de linha de comando → são acessados por → ponteiros na pilha
- Chamada de sistema → usa → registradores para número e argumentos
- Arquivo aberto → é identificado por → descritor de arquivo
- Descritor de arquivo → abstrai → recurso de entrada/saída

## Elementos centrais

| Elemento | Papel |
|---|---|
| pilha inicial | guarda contagem e ponteiros para argumentos |
| registradores | transportam número da syscall e parâmetros |
| descritor de arquivo | identifica arquivos e streams abertos |
| código de retorno | informa sucesso ou erro ao chamador |

## Ver também

- [[Chamada do Sistema]]
- [[Descritores de Arquivo UNIX]]
- [[Argumentos de Linha de Comando em C]]
- [[Processo]]

## Nota de integração

criado pelo Codex
