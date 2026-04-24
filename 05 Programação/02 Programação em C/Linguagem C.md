---
title: Linguagem C
aliases:
  - C
  - linguagem de programação C
  - ANSI C
  - K&R
  - Dennis Ritchie
tags:
  - computação/fundamentos
  - computação/histórico
date: 2026-04-06
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Programação]]

# Linguagem C

## Definição
Linguagem de programação de **nível 5** ([[Níveis de Abstração]]) de propósito geral, criada especificamente para programação de sistemas. É a linguagem de referência para [[Sistema de Compilação|sistemas de compilação]] e a base do [[Sistema Operacional]] Unix.

## Relações (SPO)
- C → criada por → Dennis Ritchie (Bell Labs, 1969–1973)
- C → projetada para → implementar o [[Sistema Operacional]] Unix
- C → padronizada por → ANSI (1989) e ISO
- C → usada em → programação de sistemas, kernels de SO, firmware
- C → precede → C++ e Java (que herdam e estendem sua sintaxe)

---

## Origem histórica

| Fato | Detalhe |
|---|---|
| **Criação** | Dennis Ritchie, Bell Laboratories, 1969–1973 |
| **Motivação** | Linguagem de programação de sistemas para o Unix |
| **Padronização** | ANSI C ratificado em 1989; depois transferido à ISO |
| **Referência** | Livro *The C Programming Language* — Kernighan e Ritchie ("K&R"), 261 páginas |

> [!quote] Dennis Ritchie sobre C
> "Quirky, flawed, and an enormous success." ("Excêntrica, imperfeita e um enorme sucesso.")

### Linhagem: BCPL → B → C

```
BCPL (Martin Richards)
    ↓  influência indireta
B (Ken Thompson, 1970 — primeiro Unix no DEC PDP-7)
    ↓
C (Dennis Ritchie, Bell Labs, 1969–1973)
```

| Linguagem | Autor | Ano | Característica-chave |
|---|---|---|---|
| **BCPL** | Martin Richards | ~1967 | Sem tipos (*typeless*) |
| **B** | Ken Thompson | 1970 | Sem tipos; escrita para o PDP-7 |
| **C** | Dennis Ritchie | 1969–73 | **Introduz hierarquia de tipos** |

BCPL e B são linguagens **sem tipo** (*typeless*): uma única unidade de dado é a palavra da máquina. C rompe com isso ao introduzir tipos fundamentais (caracteres, inteiros de vários tamanhos, ponto flutuante) e derivados (ponteiros, arrays, estruturas, uniões).

### Por que C teve tanto sucesso?

1. **Ligação profunda com o Unix:** o kernel do Unix e todas as suas ferramentas foram escritos em C. Quando o Unix se popularizou nas universidades no final dos anos 1970, C veio junto.

2. **Portabilidade do Unix:** como o Unix era escrito em C, podia ser portado para novas arquiteturas com relativa facilidade — ampliando a audiência de ambos.

3. **Linguagem pequena e simples:** projetada por uma única pessoa (não por comitê) → design limpo e consistente. O livro K&R descreve a linguagem completa em apenas 261 páginas.

4. **Propósito prático:** não foi criada para ser elegante teoricamente — foi criada para fazer o trabalho. Programadores encontraram nela o que precisavam sem obstáculos.

---

## Estrutura de um programa C

Todo programa C, independentemente do tamanho, consiste em **funções** e **variáveis**:
- **Funções** contêm declarações que especificam as operações a serem realizadas
- **Variáveis** armazenam valores usados durante a computação

```c
#include <stdio.h>          /* 1. inclui declarações da biblioteca padrão de E/S */

main()                      /* 2. define a função main — ponto de entrada obrigatório */
{                           /* 3. corpo da função entre chaves */
    printf("hello, world\n");  /* 4. chama função da biblioteca padrão */
}
```

**Regras fundamentais:**
- Todo programa deve ter uma função `main` — a execução começa ali
- `main` pode chamar outras funções (definidas pelo programador ou da biblioteca)
- `#include <stdio.h>` — diretiva do pré-processador para incluir declarações de E/S padrão
- Declarações de variáveis vêm antes de qualquer instrução executável na função
- Cada instrução termina com `;`

**Compilação no Unix:**
```
cc hello.c    →    a.out    →    ./a.out
```
Com gcc: `gcc hello.c -o hello`. Ver [[Sistema de Compilação]] para o pipeline completo.

## Características

- **Linguagem de sistemas:** acesso direto à [[Memória]], [[Registrador|registradores]] e hardware — recursos indispensáveis para SOs e firmware
- **Portável:** com o compilador [[#GNU — as ferramentas de C para o mundo livre|gcc]], código C pode ser compilado para praticamente qualquer arquitetura ([[Intel x86]], [[ARM]], [[AVR]] etc.)
- **Base enorme de código:** décadas de sistemas, ferramentas e aplicações escritas em C

## Controle de fluxo

C oferece as construções fundamentais para programas bem estruturados:

| Construção | Uso | Característica |
|---|---|---|
| `if (cond) ... else ...` | Decisão | — |
| `while (cond) { ... }` | Laço com teste no topo | Não executa se falso desde o início |
| `for (init; cond; inc) { ... }` | Laço compacto | init uma vez; cond testada antes de cada iteração; inc após cada corpo |
| `do { ... } while (cond)` | Laço com teste no fundo | Executa ao menos uma vez |
| `switch (expr) { case ... }` | Seleção entre múltiplos valores | — |
| `break` | Saída antecipada de laço/switch | — |

### `for` vs `while`

```c
/* equivalentes: */
for (fahr = 0; fahr <= 300; fahr = fahr + 20)
    printf(...);

fahr = 0;
while (fahr <= 300) {
    printf(...);
    fahr = fahr + 20;
}
```

`for` é preferível quando inicialização, condição e incremento são logicamente relacionados — mantém o controle do laço em um único lugar.

### Atribuição como expressão

Em C, **qualquer atribuição é uma expressão** com valor igual ao lado esquerdo após a atribuição. Isso permite o idioma clássico de leitura de caracteres:

```c
while ((c = getchar()) != EOF)   /* lê, atribui a c, testa EOF */
    putchar(c);
```

Os parênteses em `(c = getchar())` são **obrigatórios**: `!=` tem precedência maior que `=`.

Atribuições são **associativas à direita**, o que permite encadeamento:

```c
nl = nw = nc = 0;
/* equivalente a: nl = (nw = (nc = 0)); */
/* as três variáveis recebem 0 */
```

### Operadores lógicos `&&` e `||`

| Operador | Significado | Precedência |
|---|---|---|
| `&&` | AND lógico | Maior (avaliado antes de `\|\|`) |
| `\|\|` | OR lógico | Menor |

**Avaliação em curto-circuito (*short-circuit evaluation*):**

Expressões conectadas por `&&` ou `||` são avaliadas da esquerda para a direita, e a avaliação **para assim que o resultado for conhecido**:

```c
if (c == ' ' || c == '\n' || c == '\t')
```

- Se `c == ' '` for verdadeiro, os outros dois testes **não são executados** — o resultado já é `true`
- Se o primeiro operando de `&&` for falso, o segundo **nunca é avaliado**

> [!tip] Por que isso importa
> Curto-circuito não é apenas otimização — permite construções como `if (p != NULL && *p == 0)` onde dereferênciar `p` seria erro se `p` fosse nulo. A ordem dos operandos tem significado semântico.

---

## Filosofia de design

C é uma linguagem deliberadamente **de baixo nível** — no sentido neutro: lida com os mesmos objetos que a maioria dos computadores manipula diretamente (caracteres, números, endereços), combinados com operadores aritméticos e lógicos implementados em hardware real.

> **O programador sabe o que está fazendo.** C exige que as intenções sejam declaradas explicitamente, mas não impõe restrições desnecessárias. Isso é poder e responsabilidade ao mesmo tempo.

### O que C deliberadamente NÃO oferece

C é pequena por design — e isso é uma vantagem. O que está ausente:

| Ausência | Consequência prática |
|---|---|
| Operações em strings/arrays completos | Manipulação de strings exige chamadas de função (`strcmp`, `strcpy`, etc.) |
| Heap nativo / coleta de lixo | Alocação dinâmica via `malloc`/`free` — responsabilidade do programador |
| E/S nativa (`READ`/`WRITE`) | Todo I/O via chamadas explícitas à biblioteca padrão (`printf`, `fread` etc.) |
| Multiprogramação / paralelismo | Sem threads ou corrotinas na linguagem — fornecidas pelo SO via chamadas de sistema |

> [!tip] Benefício do minimalismo
> Por ser pequena, C pode ser descrita em pouco espaço, aprendida rapidamente, e o programador pode conhecer e usar regularmente a linguagem **inteira** — algo impossível em linguagens maiores.

## Limitações
- **Ponteiros:** principal fonte de confusão e erros de programação (acesso indevido à memória, buffer overflows)
- **Sem abstrações modernas:** sem classes, objetos ou exceções nativas
- **Verificação de tipos fraca historicamente:** a evolução fortaleceu o type-checking (ANSI C eliminou a permissividade original de misturar ponteiros e inteiros), mas C ainda é menos restritiva que linguagens modernas
- C++ e Java foram criados para endereçar essas limitações em contextos de programação de aplicações

---

## ANSI C — As duas contribuições principais (1983–1988)

Em 1983, o ANSI estabeleceu um comitê para criar uma definição moderna e abrangente de C. O padrão foi concluído em 1988 (frequentemente referido como "ANSI C" ou "C89").

### 1ª contribuição: nova sintaxe de declaração de funções

A mudança mais importante para programadores: declarações de função passam a incluir **a descrição dos argumentos**.

```c
/* Antes (K&R original) */
int max(a, b)
int a, b;
{ ... }

/* Depois (ANSI C) */
int max(int a, int b) { ... }
```

Isso permite que compiladores detectem erros de tipo em chamadas de função — antes, argumentos incorretos passavam silenciosamente.

### 2ª contribuição: biblioteca padrão definida formalmente

O padrão especifica uma **biblioteca padrão** (acessível via headers padronizados) com funções para:
- Acesso ao SO (ler/escrever arquivos)
- E/S formatada (`printf`, `scanf`)
- Alocação de memória (`malloc`, `free`)
- Manipulação de strings

Programas que usam apenas a biblioteca padrão são **portáveis** — funcionam sem modificação em qualquer sistema que suporte C. A biblioteca é modelada na "standard I/O library" do Unix.

---

## GNU — as ferramentas de C para o mundo livre

O **Projeto GNU** (*GNU's Not Unix*) foi fundado por **Richard Stallman** em 1984 como organização sem fins lucrativos, com a meta ambiciosa de criar um sistema Unix-like completo com código-fonte **livre** (no sentido de liberdade, não de gratuidade).

| Ferramenta GNU | Função |
|---|---|
| **gcc** | Compilador C/C++/Fortran/Java/Ada (driver do [[Sistema de Compilação]]) |
| **gdb** | Depurador (*debugger*) |
| **emacs** | Editor de texto programável |
| **as** | Assembler |
| **ld** | Linker |
| **binutils** | Utilitários para manipulação de binários |

### Relação com Linux

O **kernel Linux** (Linus Torvalds) + as **ferramentas GNU** = o sistema que chamamos de "Linux". O movimento open-source moderno deve suas raízes intelectuais ao conceito de *software livre* do GNU. O Linux deve grande parte de sua popularidade às ferramentas GNU que formam seu ambiente de desenvolvimento.

> [!info] "Free as in speech, not free beer"
> A liberdade do GNU refere-se ao direito de usar, estudar, modificar e distribuir o software — não necessariamente ao preço zero.

---

## Ver também
- [[Sistema de Compilação]] — pipeline completo de compilação de C; `gcc` é o compilador GNU
- [[Sistema Operacional]] — Unix, para o qual C foi criado; BCPL→B→C no contexto do PDP-7
- [[Níveis de Abstração]] — C ocupa o nível 5 (linguagem de alto nível)
- [[História da Computação]] — contexto histórico de Unix e Bell Labs
- [[Chamada do Sistema]] — C acessa recursos do SO via chamadas de sistema (I/O, alocação de memória)
