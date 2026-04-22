---
title: Tipos de Dados C
aliases:
  - tipos primitivos C
  - int
  - float
  - char
  - double
  - short
  - long
  - tipo C
tags:
  - computação/fundamentos
date: 2026-04-07
---

# Tipos de Dados C

## Definição
C fornece uma **hierarquia de tipos de dados**, distinguindo-se das linguagens anteriores ([[Linguagem C#Linhagem: BCPL → B → C|BCPL e B]]) que eram *typeless*. A escolha do tipo determina a representação binária, o tamanho em [[Memória]] e as operações disponíveis.

## Relações (SPO)
- Tipos de Dados C → mapeiam para → representações em [[Sistemas de Representação]] (signed, unsigned, IEEE 754)
- Tipos de Dados C → têm tamanho → dependente da arquitetura (exceto `char = 1 byte`)
- Tipos de Dados C → armazenados em → [[Memória]] como sequências de bytes

---

## Tipos primitivos

### Tipos inteiros

| Tipo | Tamanho típico | Faixa (signed) | Correspondência |
|---|---|---|---|
| `char` | **1 byte** (fixo) | −128 a +127 | [[ASCII]] — caractere ou inteiro pequeno |
| `short` | 2 bytes | −32.768 a +32.767 | [[Sistemas de Representação#Signed (sinalizado)\|Signed 16 bits]] |
| `int` | 2 ou 4 bytes* | −32.768 a +32.767 (16-bit) / −2.147.483.648 a +2.147.483.647 (32-bit) | Tipo inteiro "natural" da máquina |
| `long` | 4 ou 8 bytes* | Depende da arquitetura | — |

*O tamanho de `int` e `long` é **dependente da máquina**. Em x86-64 (Linux moderno): `int` = 4 bytes, `long` = 8 bytes. Ver [[Memória#Implementação física — DRAM|tabela de tamanhos em x86-64]].

> [!warning] `int` não tem tamanho fixo
> O K&R especifica que `int` pode ter 16 ou 32 bits dependendo da arquitetura. Em sistemas modernos de 64 bits, `int` é tipicamente 32 bits — mas código portável não deve assumir isso.

Todos os tipos inteiros possuem variantes `unsigned`:

| Tipo | Faixa unsigned (16-bit) | Faixa unsigned (32-bit) |
|---|---|---|
| `unsigned char` | 0 a 255 | — |
| `unsigned short` | 0 a 65.535 | — |
| `unsigned int` | 0 a 65.535 | 0 a 4.294.967.295 |

### Tipos de ponto flutuante

| Tipo | Tamanho | Precisão | Faixa de magnitude | IEEE 754 |
|---|---|---|---|---|
| `float` | 32 bits | ≥ 6 dígitos significativos | ~10⁻³⁸ a 10³⁸ | Precisão simples |
| `double` | 64 bits | ≥ 15 dígitos significativos | ~10⁻³⁰⁸ a 10³⁰⁸ | Precisão dupla |

> [!info] `float` e `double` correspondem ao [[Sistemas de Representação#Formatos IEEE 754|IEEE 754 precisão simples e dupla]].

---

## Tipos derivados

Construídos a partir dos tipos primitivos:

| Tipo derivado | Descrição |
|---|---|
| `array` | Sequência de elementos do mesmo tipo |
| `struct` | Agrupamento de campos de tipos diferentes |
| `union` | Variantes que compartilham o mesmo espaço de memória |
| `pointer` (`*`) | Endereço de memória de um objeto de outro tipo |
| funções | Retornam valores de tipos básicos, structs, uniões ou ponteiros |

---

## Aritmética e conversão de tipos

### Truncamento na divisão inteira

> **Divisão entre dois inteiros trunca a parte fracionária — sem arredondamento.**

```c
int x = 5 / 9;    /* resultado: 0, não 0.555... */
```

Consequência prática: para converter Fahrenheit → Celsius corretamente em C:

```c
/* Errado — trunca para 0: */
celsius = 5/9 * (fahr - 32);

/* Correto — usa ponto flutuante: */
celsius = (5.0/9.0) * (fahr - 32.0);
```

### Promoção automática int → float

Quando uma operação aritmética tem **um operando inteiro e um de ponto flutuante**, o inteiro é automaticamente convertido para ponto flutuante antes da operação:

```c
float resultado = 5.0 / 2;    /* 2 é convertido para 2.0 → resultado: 2.5 */
```

Uma constante com ponto decimal explícito (ex: `5.0`) é sempre ponto flutuante, independentemente do valor.

---

## Declaração de variáveis

Em C, **todas as variáveis devem ser declaradas antes de serem usadas**, normalmente no início da função:

```c
int fahr, celsius;          /* duas variáveis inteiras */
float lower, upper, step;   /* três variáveis float */
```

Uma declaração anuncia o **nome** e o **tipo** da variável. Declarações terminam com `;`.

---

## printf — especificadores de formato

Cada tipo em C tem especificadores correspondentes para `printf` (e `scanf`):

| Especificador | Tipo | Exemplo | Saída |
|---|---|---|---|
| `%d` | `int` (decimal) | `printf("%d", 42)` | `42` |
| `%ld` | `long` (decimal) | `printf("%ld", nc)` | valor longo |
| `%f` | `float` / `double` (ponto flutuante) | `printf("%f", 3.14)` | `3.140000` |
| `%o` | `int` (octal) | `printf("%o", 8)` | `10` |
| `%x` | `int` (hexadecimal) | `printf("%x", 255)` | `ff` |
| `%c` | `char` (caractere) | `printf("%c", 'A')` | `A` |
| `%s` | string (ponteiro para `char`) | `printf("%s", "oi")` | `oi` |
| `%%` | literal `%` | `printf("100%%")` | `100%` |

### Largura e precisão

```
%6d      → inteiro, mínimo 6 caracteres de largura (alinhado à direita)
%3.0f    → float, mínimo 3 de largura, 0 casas decimais
%6.1f    → float, mínimo 6 de largura, 1 casa decimal
%.2f     → float, 2 casas decimais (largura não restringida)
%6f      → float, mínimo 6 de largura
```

---

## EOF e o tipo `int` para leitura de caracteres

`getchar()` retorna o próximo caractere da entrada como um `int`. Quando não há mais entrada, retorna **EOF** — um valor sentinela definido em `<stdio.h>`.

**Por que `c` deve ser `int`, não `char`?**

- `char` pode representar apenas valores de −128 a +127 (ou 0 a 255 em `unsigned char`)
- EOF tem um valor inteiro que **não pode ser confundido com nenhum char válido** (tipicamente −1)
- Se `c` fosse `char`, EOF poderia ser truncado e confundido com um caractere real

```c
int c;                           /* deve ser int, não char */
while ((c = getchar()) != EOF)   /* idioma clássico C */
    putchar(c);
```

> [!note] Precedência de operadores
> Os parênteses em `(c = getchar())` são necessários: `!=` tem precedência maior que `=`.
> Sem eles: `c = getchar() != EOF` ≡ `c = (getchar() != EOF)` — c receberia 0 ou 1, não o caractere.

---

## Constantes de caractere

Um caractere entre aspas simples representa o **valor inteiro** desse caractere no conjunto de caracteres da máquina ([[ASCII]]):

| Notação | Valor (ASCII) | Equivalente |
|---|---|---|
| `'A'` | 65 | Letra maiúscula A |
| `'a'` | 97 | Letra minúscula a |
| `'0'` | 48 | Dígito zero |
| `'\n'` | 10 | Newline |
| `'\t'` | 9 | Tab |

`'A'` é preferível a `65` — o significado é óbvio e independe do conjunto de caracteres específico.

> [!warning] `'\n'` ≠ `"\n"`
> `'\n'` é uma **constante de caractere** (inteiro de valor 10). `"\n"` é uma **constante de string** (array de chars) que contém um caractere e o terminador nulo `\0`.

---

## Operações bit a bit em C

C suporta operações booleanas diretamente sobre qualquer tipo integral.

### Operadores bitwise

| Operador | Operação | Exemplo (char) | Resultado |
|---|---|---|---|
| `~` | NOT (complemento) | `~0x41` | `0xBE` |
| `&` | AND | `0x69 & 0x55` | `0x41` |
| `\|` | OR | `0x69 \| 0x55` | `0x7D` |
| `^` | XOR | `0x69 ^ 0x55` | `0x3C` |
| `<<` | shift esquerda | `x << k` | `x * 2^k` (descarta bits altos) |
| `>>` | shift direita | `x >> k` | lógico (unsigned) ou aritmético (signed) |

### Bitwise vs. lógico — diferença crítica

| Expressão | Tipo | Resultado para `a=0x55, b=0x46` |
|---|---|---|
| `a & b` | bitwise | `0x44` |
| `a && b` | lógico | `0x01` (verdadeiro/falso) |
| `a \| b` | bitwise | `0x57` |
| `a \|\| b` | lógico | `0x01` |
| `!a` | lógico NOT | `0x00` (a ≠ 0) |
| `~a` | bitwise NOT | `0xAA` |

> [!warning] Curto-circuito lógico
> `&&` e `||` não avaliam o segundo operando se o resultado já é determinado pelo primeiro: `a && 5/a` nunca divide por zero; `p && *p++` nunca desreferencia ponteiro nulo.

### Mascaramento

Padrão comum: isolar ou manipular subconjunto de bits com máscara.

```c
x & 0xFF          /* byte menos significativo de x; demais zerados */
x ^ ~0xFF         /* complementa todos os bytes exceto o menos significativo */
x | 0xFF          /* byte menos significativo = todos 1s */
~0               /* máscara de todos 1s — independente do word size */
(1 << k) - 1     /* k bits inferiores = 1 (máscara de k bits) */
```

### Shift em C

```c
/* Shift esquerda: mesmo para signed e unsigned */
x << k    /* x * 2^k; bits altos descartados */

/* Shift direita unsigned: sempre lógico (zero fill) */
(unsigned) x >> k    /* x / 2^k, arredonda para baixo */

/* Shift direita signed: aritmético na maioria dos compiladores */
(int) x >> k    /* copia bit de sinal; ≡ ⌊x / 2^k⌋ */
```

> [!warning] Shift por k ≥ word size é comportamento indefinido em C. Resultado depende da implementação (muitos usam `k mod w`).

### Promoção signed → unsigned em expressões mistas

Quando expressão mistura `int` e `unsigned`, C converte `int` para `unsigned`:

```c
-1 < 0U      /* falso: -1 vira 4294967295U */
2147483647U > -2147483647-1   /* falso: TMin vira 2147483648U */
```

> [!danger] Bug clássico: `length` unsigned em loop
> ```c
> float sum_elements(float a[], unsigned length) {
>     for (int i = 0; i <= length-1; i++)   /* se length=0, length-1 = UMax! */
>         result += a[i];
> }
> ```
> `0 - 1` em unsigned = `UMax`. Fix: usar `i < length` ou declarar `length` como `int`.

---

## Operadores de incremento e decremento

| Operador | Significado | Equivalente |
|---|---|---|
| `++nc` (prefixo) | Incrementa `nc` antes de usar o valor | `nc = nc + 1` |
| `nc++` (sufixo) | Usa o valor, depois incrementa | `nc = nc + 1` |
| `--nc` / `nc--` | Decrementa por 1 | `nc = nc - 1` |

`++nc` e `nc++` produzem o mesmo efeito quando usados como instrução isolada. A diferença aparece em expressões: `++nc` retorna o valor *após* o incremento; `nc++` retorna o valor *antes*.

---

## Ver também
- [[Sistemas de Representação]] — unsigned, signed, IEEE 754; T2U/U2T; extensão/truncamento
- [[Aritmética Inteira]] — overflow, otimização por shift, vulnerabilidades de segurança
- [[Álgebra Booleana]] — fundamento matemático das operações bit a bit
- [[Memória]] — tamanhos dos tipos em x86-64 (Linux)
- [[Palavras Binárias]] — byte, word, dword, qword; como inteiros são armazenados
- [[ASCII]] — char como representação de caractere; sequências de escape C
- [[Linguagem C]] — contexto geral da linguagem
