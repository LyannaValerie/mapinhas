---
title: Atributos unsequenced e reproducible em C
aliases:
  - unsequenced C
  - reproducible C
  - atributos de função C23
tags:
  - computação/programação/c
source: Jens Gustedt, Modern C, seção 16.3; É. Alepins e J. Gustedt, Unsequenced functions, WG14 N2956, 2022
date: 2026-04-24
created_by: Codex
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Programação]]

# Atributos unsequenced e reproducible em C

## Definição

`[[unsequenced]]` e `[[reproducible]]` são atributos de função que comunicam propriedades de repetibilidade e efeitos observáveis ao compilador. Eles ajudam a mover, combinar ou eliminar chamadas quando o contrato é verdadeiro.

## Relações (SPO)
- `[[unsequenced]]` → modela → chamada sem dependência de estado externo observável
- `[[reproducible]]` → exige → resultado repetível para mesmos argumentos
- `[[reproducible]]` → permite → restauração de estado temporariamente modificado
- atributo de função → aumenta → conhecimento local do compilador
- parâmetro ponteiro → deve explicitar → `restrict` quando houver independência exigida

---

## Diferença prática

`[[unsequenced]]` é mais forte: a chamada não deve depender de estado global mutável nem alterar estado observável. `[[reproducible]]` é mais fraco: a função pode modificar estado temporariamente se restaurar o valor original antes de retornar.

## Escopo local

Pragmas e atributos acumulam dentro do escopo atual. Isso permite fornecer garantias locais ao compilador sem alterar todos os usos globais da função.

## Ponteiros

Esses atributos podem ser úteis mesmo com parâmetros ponteiro, desde que os ponteiros obedeçam restrições semelhantes às de `restrict`. A anotação explícita com `restrict` documenta a restrição para leitores e chamadores.

## Ver também

- [[Restrict em C]]
- [[Funções em C]]
- [[Performance em C]]
- [[Modelo de Memória em C]]

## Nota de integração

criado pelo Codex

