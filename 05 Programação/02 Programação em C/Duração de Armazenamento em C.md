---
title: Duração de Armazenamento em C
aliases:
  - storage duration C
  - lifetime C
  - tempo de vida em C
tags:
  - computação/fundamentos
source: Jens Gustedt, material didático sobre C23
date: 2026-04-23
created_by: Codex
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Programação]]

# Duração de Armazenamento em C

## Definição

Período durante o qual o armazenamento de um objeto existe. Não é igual a escopo: escopo trata visibilidade do nome; duração trata existência do objeto.

## Relações (SPO)
- Escopo → controla → visibilidade de identificador
- Duração de armazenamento → controla → existência do objeto
- Objeto automático → existe durante → execução do bloco
- Objeto estático → existe durante → todo o programa
- Objeto alocado → existe até → liberação explícita

---

## Categorias principais

| Duração | Exemplo | Observação |
|---|---|---|
| automática | variável local sem `static` | termina ao sair do bloco |
| estática | global ou local `static` | existe durante todo o programa |
| alocada | `malloc` | termina com `free` |
| thread-local | `_Thread_local` | uma instância por thread |

## Erro clássico

Retornar ponteiro para objeto automático cria ponteiro pendente:

```c
int *bad(void) {
    int x = 1;
    return &x;
}
```

Depois do retorno, `x` não existe mais.

## Ver também

- [[Escopo em C]]
- [[Variáveis Estáticas em C]]
- [[Alocação Dinâmica de Memória em C]]

## Nota de integração

criado pelo Codex

