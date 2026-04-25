---
title: Limpeza com goto em C
aliases:
  - cleanup goto C
  - tratamento de erro com goto C
  - labels em C
tags:
  - computação/programação/c
source: Jens Gustedt, Modern C, capítulo 15
date: 2026-04-24
created_by: Codex
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Programação]]

# Limpeza com goto em C

## Definição

Uso disciplinado de `goto` para concentrar liberação de recursos em um único caminho de saída de função. É padrão comum em C quando múltiplas aquisições podem falhar parcialmente.

## Relações (SPO)
- falha intermediária → salta para → bloco de limpeza
- bloco de limpeza → libera → recursos já adquiridos
- label de `goto` → é visível em → toda a função
- `goto` → só pode saltar dentro de → mesma função
- retorno de operação falível → deve ser → verificado

---

## Padrão

```c
int f(void) {
    int rc = -1;
    FILE *fp = fopen("x", "rb");
    if (!fp) goto out;

    char *buf = malloc(4096);
    if (!buf) goto close_file;

    rc = 0;
    free(buf);
close_file:
    fclose(fp);
out:
    return rc;
}
```

## Regra operacional

O salto não deve esconder lógica normal. Use para erro e cleanup quando isso reduz duplicação e torna ordem de liberação verificável.

## Ver também

- [[Falhas em Programas C]]
- [[Arquivos em C]]
- [[Alocação Dinâmica de Memória em C]]

## Nota de integração

criado pelo Codex
