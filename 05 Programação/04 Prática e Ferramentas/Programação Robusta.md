---
title: Programação Robusta
aliases:
  - robustez de programa
  - programa robusto
tags:
  - computação/fundamentos
source: Programming from the Ground Up (pgubook), metadados bibliográficos completos ausentes no texto local
created_by: Codex
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Programação]]

# Programação Robusta

## Definição

Programação robusta é prática de projetar código para lidar com erro, entrada inválida, recurso indisponível e uso inesperado sem falhar de modo descontrolado.

## Relações (SPO)
- Programa robusto → trata → condições de erro
- Estimativa ruim → aumenta → risco de entrega
- Validação de entrada → reduz → falhas por dados inválidos
- Tratamento de erro → exige → caminho de recuperação ou encerramento claro
- Código robusto → depende de → disciplina de checagem

## Princípios

- checar retornos de operações que podem falhar
- validar entrada na fronteira do programa
- isolar responsabilidades em funções menores
- reportar erro no nível correto
- manter caminho de limpeza para recursos adquiridos

## Ver também

- [[Falhas em Programas C]]
- [[Limpeza com goto em C]]
- [[Biblioteca assert.h]]
- [[Arquivos em C]]

## Nota de integração

criado pelo Codex
