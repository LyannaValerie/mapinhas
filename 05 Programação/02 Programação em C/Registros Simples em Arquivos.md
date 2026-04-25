---
title: Registros Simples em Arquivos
aliases:
  - registros fixos em arquivo
  - arquivo de registros simples
tags:
  - computação/fundamentos
source: Programming from the Ground Up (pgubook), metadados bibliográficos completos ausentes no texto local
created_by: Codex
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Programação]]

# Registros Simples em Arquivos

## Definição

Registro simples em arquivo é uma unidade persistente com campos de tamanho e posição previsíveis. Em baixo nível, o programa grava e lê blocos de bytes; o significado vem do layout escolhido pelo programador.

## Relações (SPO)
- Registro → agrupa → campos relacionados
- Layout fixo → permite → cálculo direto de deslocamento
- Arquivo → preserva → dados após fim do processo
- Leitura de registro → exige → mesmo layout usado na escrita
- Campo textual → pode usar → terminador nulo ou preenchimento

## Modelo operacional

Arquivos não armazenam “objetos” por si mesmos. Eles armazenam bytes. Nome, sobrenome, endereço ou idade só existem como estrutura porque o programa define tamanho, ordem e codificação dos campos.

## Risco

Mudança de layout quebra compatibilidade com arquivos já gravados, salvo se houver versão, conversão ou camada de leitura compatível.

## Ver também

- [[Arquivos em C]]
- [[Descritores de Arquivo UNIX]]
- [[Dados Binários]]
- [[ASCII]]

## Nota de integração

criado pelo Codex
