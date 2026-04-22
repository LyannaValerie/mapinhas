---
title: Escopo em C
aliases:
  - variáveis automáticas C
  - variáveis externas C
  - escopo de variável C
  - extern C
  - variável global C
  - variável local C
tags:
  - computação/fundamentos
date: 2026-04-21
---

# Escopo em C

## Definição

Escopo determina onde uma variável existe e é visível no programa. C distingue dois tipos fundamentais: **automáticas** (locais) e **externas** (globais).

## Relações (SPO)
- Variável automática → existe apenas durante → execução da função que a declara
- Variável externa → persiste durante → toda a execução do programa
- `extern` → declara → variável definida em outro arquivo (sem alocar armazenamento)
- Variável automática não inicializada → contém → lixo

---

## Variáveis automáticas

Declaradas dentro de uma função. Existem apenas enquanto a função está em execução.

```c
int getline(char s[], int lim)
{
    int c, i;    /* c e i são automáticas: criadas ao entrar, destruídas ao sair */
    ...
}
```

- Invisíveis a qualquer outra função
- Não retêm valor entre chamadas — devem ser inicializadas explicitamente a cada entrada
- Se não inicializadas: contêm **lixo** (valor indefinido)
- A variável `i` em `getline` é completamente independente de qualquer `i` em outra função

> [!info] Terminologia
> Variáveis automáticas seguem a terminologia de outras linguagens. O modificador `static` faz uma variável local **reter** seu valor entre chamadas.

---

## Variáveis externas

Definidas fora de qualquer função. Acessíveis a qualquer função no mesmo arquivo (ou em outros arquivos com declaração `extern`).

```c
int max;                  /* definição: aloca armazenamento */
char line[MAXLINE];
char longest[MAXLINE];

int getline(void);
void copy(void);

main()
{
    int len;
    extern int max;       /* declaração: diz que max é externo */
    extern char longest[];
    ...
}
```

### Definição vs. declaração

| Termo | O que faz | Exemplo |
|---|---|---|
| **Definição** | Cria a variável; aloca armazenamento | `int max;` fora de função |
| **Declaração** | Anuncia o tipo; **não** aloca armazenamento | `extern int max;` dentro de função |

Variável externa deve ser **definida exatamente uma vez** fora de qualquer função. Pode ser **declarada** com `extern` em quantas funções/arquivos precisar.

### Quando `extern` pode ser omitido

Se a definição da variável externa ocorre **antes** de seu uso no arquivo-fonte, a declaração `extern` dentro da função é redundante. Prática comum: colocar todas as definições externas no início do arquivo.

```c
int max = 0;          /* definição — antes de main */
char line[MAXLINE];
char longest[MAXLINE];

main() {              /* max, line, longest visíveis aqui sem extern */
    ...
}
```

---

## Múltiplos arquivos

Se a variável é definida em `file1.c` e usada em `file2.c` e `file3.c`:

```c
/* file1.c */
int shared_var;     /* definição */

/* file2.c */
extern int shared_var;   /* declaração — necessária */

/* file3.c */
extern int shared_var;   /* declaração — necessária */
```

Prática: colocar declarações `extern` em um **header** (`.h`) incluído por todos os arquivos que precisam das variáveis.

---

## Variáveis externas: vantagens e perigos

### Vantagem
- Comunicação entre funções sem listas de argumentos extensas
- Persistem entre chamadas

### Perigos

> [!warning] Uso excessivo de variáveis externas
> Dependência pesada em variáveis externas leva a programas com **conexões de dados não óbvias** — variáveis podem ser alteradas de forma inesperada ou inadvertida. Código torna-se difícil de modificar e depurar.
>
> Variáveis externas são **sempre** visíveis, mesmo quando não deveriam ser.
>
> Preferir: passar dados explicitamente como argumentos sempre que possível. Variáveis externas como último recurso.

---

## Inicialização padrão

| Tipo de variável | Inicialização padrão |
|---|---|
| Externa | Zero (por padrão) |
| Estática local | Zero (por padrão) |
| Automática | **Indefinida** (lixo) |

---

## `void` em protótipos sem argumentos

Para compatibilidade com C antigo, `()` em protótipo significa "lista de argumentos antiga" (sem verificação). Para lista explicitamente vazia, usar `void`:

```c
int getline(void);    /* correto: função sem argumentos */
int getline();        /* ANSI: old-style, desativa verificação de args */
```

---

## Arquivos de cabeçalho (4.5)

Em programas grandes divididos em vários arquivos, centralizar declarações compartilhadas em um header `.h`:

```
main.c     → #include "calc.h"
stack.c    → #include "calc.h"
getop.c    → #include "calc.h"
getch.c    → #include "calc.h"
```

`calc.h` contém: `#define`s, protótipos de funções, declarações `extern`. Cada arquivo inclui o que precisa. Trade-off: quanto maior o programa, mais se justifica dividir em vários headers. Para programas moderados, um header único é suficiente.

---

## Variáveis register (4.7)

`register` aconselha o compilador a manter a variável em registrador de máquina — pode gerar código menor/mais rápido. Compiladores são livres para ignorar.

```c
register int x;
register char c;

f(register unsigned m, register long n)
{
    register int i;
    ...
}
```

Restrições:
- Somente para variáveis automáticas e parâmetros formais
- Poucos registradores disponíveis por função; excesso é ignorado sem erro
- Não é possível tirar endereço (`&`) de variável `register`
- Tipos e quantidade permitidos variam por máquina

> [!info]
> Compiladores modernos geralmente otimizam melhor que dicas manuais de `register`. O modificador é praticamente obsoleto.

---

## Estrutura de blocos (4.8)

C não permite funções dentro de funções, mas permite declarar variáveis dentro de qualquer bloco composto (`{ }`):

```c
if (n > 0) {
    int i;              /* novo i, local ao bloco */
    for (i = 0; i < n; i++)
        ...
}
/* i não existe aqui */
```

- Variável declarada em bloco interno oculta variável de mesmo nome em escopo externo
- Existe até o `}` correspondente
- Inicializada a cada entrada no bloco (automática)

```c
int x;
int y;
f(double x)        /* parâmetro x oculta externo int x */
{
    double y;      /* local y oculta externo int y */
}
```

> [!warning]
> Ocultar nomes de escopos externos é fonte de confusão — evitar exceto quando necessário.

---

## Ver também
- [[Funções em C]] — parâmetros locais; call by value; retornos não-inteiros
- [[Arrays em C]] — arrays automáticos vs. externos
- [[Linguagem C]] — estrutura geral; contexto K&R
- [[Tipos de Dados C]] — `static`, modificadores de tipo
- [[Variáveis Estáticas em C]] — `static` externo e interno
- [[Pré-processador C]] — `#include`, headers
