---
title: Restrict em C
aliases:
  - restrict C
  - ponteiro restrict C
  - qualificador restrict
tags:
  - computação/programação/c
source: Jens Gustedt, Modern C, seção 16.2
date: 2026-04-24
created_by: Codex
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Programação]]

# Restrict em C

## Definição

`restrict` qualifica ponteiro e declara que, durante o acesso relevante, aquele ponteiro fornece acesso exclusivo ao objeto apontado. O contrato reduz aliasing visível ao compilador.

## Relações (SPO)
- `restrict` → declara → acesso exclusivo
- acesso exclusivo → permite → otimização de memória
- qualificação `restrict` → impõe contrato → chamador
- `memcpy` → assume → objetos sem sobreposição
- `memmove` → aceita → objetos sobrepostos

---

## Contrato

O compilador pode assumir que alterações no objeto apontado ocorrem por aquele caminho de acesso. Se o chamador passa ponteiros sobrepostos onde a interface exige `restrict`, o programa viola o contrato da chamada.

## Exemplo operacional

`memcpy` usa ponteiros `restrict` porque presume regiões sem sobreposição. `memmove` não faz essa promessa; por isso é mais geral, mas pode carregar custo maior.

## Relação com atributos

Funções anotadas com `[[unsequenced]]` ou `[[reproducible]]` que recebem ponteiros devem explicitar `restrict` nos parâmetros quando o contrato de independência for parte da otimização pretendida.

## Ver também

- [[Ponteiros em C]]
- [[Arrays em C]]
- [[Performance em C]]
- [[Atributos unsequenced e reproducible em C]]

## Nota de integração

criado pelo Codex

