---
title: Alinhamento em C
aliases:
  - alignment C
  - alinhamento de memória C
tags:
  - computação/fundamentos
source: Jens Gustedt, material didático sobre C23
date: 2026-04-23
created_by: Codex
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Programação]]

# Alinhamento em C

## Definição

Restrição de endereço para que objeto de determinado tipo possa ser acessado corretamente. Cada tipo pode exigir que seu endereço seja múltiplo de certo valor.

## Relações (SPO)
- Tipo C → possui → requisito de alinhamento
- Objeto desalinhado → pode causar → comportamento indefinido
- `struct` → pode conter → preenchimento entre campos
- Alocador padrão → retorna → armazenamento alinhado para tipos fundamentais

---

## Alinhamento e `struct`

Compiladores podem inserir padding para respeitar alinhamento dos campos:

```c
struct s {
    char c;
    int i;
};
```

O tamanho de `struct s` pode ser maior que `sizeof(char) + sizeof(int)`.

## Ponteiros convertidos

Converter endereço bruto para ponteiro tipado só é seguro se o endereço satisfaz o alinhamento do tipo de destino. Ver [[Modelo de Memória em C]].

## Impacto prático

Alinhamento afeta:

- layout binário
- interoperabilidade com arquivos e rede
- desempenho de acesso à memória
- validade de conversões de ponteiro

## Nota de integração

criado pelo Codex

