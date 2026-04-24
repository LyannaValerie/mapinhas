---
title: Tipos Efetivos em C
aliases:
  - effective type C
  - aliasing em C
  - strict aliasing C
tags:
  - computação/fundamentos
source: Jens Gustedt, material didático sobre C23
date: 2026-04-23
created_by: Codex
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Programação]]

# Tipos Efetivos em C

## Definição

Regra que determina por qual tipo um objeto pode ser acessado. O tipo efetivo é central para otimização do compilador e para evitar aliasing inválido.

## Relações (SPO)
- Tipo efetivo → define → acesso válido a objeto
- Aliasing inválido → pode causar → comportamento indefinido
- Compilador C → usa → regras de tipo para otimização
- `unsigned char` → pode inspecionar → representação em bytes

---

## Ideia central

Depois que armazenamento recebe um valor por um tipo, acessá-lo por tipo incompatível pode violar o modelo da linguagem. Isso é diferente de apenas reinterpretar bytes em assembly: em C, o compilador pode assumir que ponteiros de tipos incompatíveis não nomeiam o mesmo objeto.

```c
int x = 1;
float *p = (float *)&x;  /* acesso incompatível se usado para ler/escrever */
```

## Relação com otimização

Regras de tipo permitem eliminar leituras redundantes e reordenar operações. Código que viola tipo efetivo pode parecer funcionar em uma compilação e falhar com otimização.

## Uso legítimo de bytes

Inspeção de representação por `unsigned char *` é caso especial comum. Ela examina bytes, não declara que o objeto mudou de tipo lógico.

## Nota de integração

criado pelo Codex

