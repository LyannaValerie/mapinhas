---
title: Argumentos de Linha de Comando em C
aliases:
  - argc argv C
  - command-line arguments C
  - argumentos main C
tags:
  - computação/fundamentos
date: 2026-04-21
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Programação]]

# Argumentos de Linha de Comando em C

## Definição

`main` pode receber argumentos da linha de comando via dois parâmetros: `argc` (contagem) e `argv` (vetor de strings). Permite que o programa se comporte diferentemente conforme argumentos fornecidos ao ser invocado.

## Relações (SPO)
- `argc` → contém → número de argumentos (incluindo nome do programa)
- `argv` → é → array de ponteiros para as strings dos argumentos
- `argv[0]` → é → nome com que o programa foi invocado
- `argv[argc]` → é → ponteiro nulo (garantido pelo padrão)

---

## Declaração de main

```c
main(int argc, char *argv[])
```

- `argc` ≥ 1 sempre (`argv[0]` é o nome do programa)
- Argumentos do usuário: `argv[1]` até `argv[argc-1]`

---

## Exemplo: echo

```
echo hello, world
```

`argc = 3`, `argv[0] = "echo"`, `argv[1] = "hello,"`, `argv[2] = "world"`.

### Versão com subscrito de array

```c
#include <stdio.h>

main(int argc, char *argv[])
{
    int i;
    for (i = 1; i < argc; i++)
        printf("%s%s", argv[i], (i < argc-1) ? " " : "");
    printf("\n");
    return 0;
}
```

### Versão com incremento de ponteiro

```c
main(int argc, char *argv[])
{
    while (--argc > 0)
        printf("%s%s", *++argv, (argc > 1) ? " " : "");
    printf("\n");
    return 0;
}
```

`++argv` avança para o próximo argumento; `*argv` derreferencia para a string.

---

## Flags opcionais por convenção UNIX

Argumento que começa com `-` introduz flag ou parâmetro opcional:

```c
/* find -x -n padrão */
main(int argc, char *argv[])
{
    int except = 0, number = 0;
    while (--argc > 0 && (*++argv)[0] == '-')
        while (c = *++argv[0])
            switch (c) {
            case 'x': except = 1; break;
            case 'n': number = 1; break;
            default:
                printf("find: opção ilegal %c\n", c);
                argc = 0;
            }
    /* após o loop: argc == 1 e *argv == padrão */
    ...
}
```

`(*++argv)[0]` — avança argv, derreferencia para string, acessa primeiro char.

---

## Ver também
- [[Ponteiros em C]] — arrays de ponteiros, `char *argv[]`
- [[Funções em C]] — `main` como função
- [[Controle de Fluxo C]] — `while`, `switch`
