---
title: Controle de Fluxo C
aliases:
  - controle de fluxo C
  - switch C
  - break C
  - continue C
  - goto C
  - do while C
  - for C
  - while C
tags:
  - computação/fundamentos
date: 2026-04-21
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Programação]]

# Controle de Fluxo C

## Definição

Estruturas de controle especificam a ordem das computações. Esta nota detalha as construções completas de C — complementando o resumo em [[Linguagem C#Controle de fluxo]].

---

## Instruções e blocos

Uma **instrução** é uma expressão seguida de `;`:

```c
x = 0;
i++;
printf("...");
```

Um **bloco** (ou instrução composta) agrupa declarações e instruções entre `{ }` — sintaticamente equivalente a uma única instrução. Sem `;` após o `}`.

---

## `if-else`

```c
if (expressão)
    instrução1
else
    instrução2
```

`else` é opcional. Quando `else` é omitido em `if` aninhados, o `else` é associado ao `if` mais próximo sem `else`:

```c
if (n > 0)
    if (a > b)
        z = a;
else           /* pertence ao 'if (a > b)', não ao 'if (n > 0)' */
    z = b;
```

Para forçar associação diferente, usar chaves:

```c
if (n > 0) {
    if (a > b)
        z = a;
} else
    z = b;
```

> [!warning] Ambiguidade de else pendente
> Indentação indica intenção ao programador, mas não ao compilador. Usar chaves sempre que houver `if` aninhados.

---

## `else if`

Sequência de decisões múltiplas — padrão idiomático:

```c
if (expressão)
    instrução
else if (expressão)
    instrução
else if (expressão)
    instrução
else
    instrução   /* default */
```

Expressões avaliadas em ordem; executa a primeira que for verdadeira. O `else` final trata o caso "nenhuma das anteriores" — pode ser omitido se não houver ação padrão.

**Exemplo — busca binária:**

```c
int binsearch(int x, int v[], int n)
{
    int low = 0, high = n - 1, mid;
    while (low <= high) {
        mid = (low + high) / 2;
        if (x < v[mid])
            high = mid - 1;
        else if (x > v[mid])
            low = mid + 1;
        else
            return mid;    /* encontrou */
    }
    return -1;
}
```

---

## `switch`

Seleção baseada em valor inteiro (ou caractere):

```c
switch (expressão) {
case const-expr: instruções
case const-expr: instruções
default:         instruções
}
```

- `case` serve apenas como **label** — execução **cai** para o próximo `case` sem `break`
- `break` sai imediatamente do `switch`
- `default` executado se nenhum `case` casa; é opcional

### Fall-through intencional

```c
switch (c) {
case '0': case '1': case '2': case '3': case '4':
case '5': case '6': case '7': case '8': case '9':
    ndigit[c-'0']++;
    break;
case ' ':
case '\n':
case '\t':
    nwhite++;
    break;
default:
    nother++;
    break;
}
```

> [!warning] Fall-through não intencional
> Esquecer `break` é fonte comum de bugs. Fall-through entre casos com código diferente deve ser comentado explicitamente.

> [!tip] `break` no último `case`
> Boa prática: colocar `break` mesmo no último `case` (mesmo desnecessário logicamente) — protege quando novos casos forem adicionados.

---

## `while`

```c
while (expressão)
    instrução
```

Testa condição **no topo**. Se falsa desde o início, o corpo nunca executa.

Preferível quando não há inicialização/incremento simples:

```c
while ((c = getchar()) == ' ' || c == '\n' || c == '\t')
    ;    /* pula espaços em branco */
```

---

## `for`

```c
for (expr1; expr2; expr3)
    instrução
```

Equivalente a:

```c
expr1;
while (expr2) {
    instrução
    expr3;
}
```

Exceto pelo comportamento de `continue` (ver abaixo).

- Qualquer das três expressões pode ser omitida (semicolons obrigatórios)
- `for (;;)` — loop infinito
- `expr1` e `expr3` geralmente atribuições ou chamadas de função
- `expr2` geralmente expressão relacional

**Exemplo — Shell sort:**

```c
void shellsort(int v[], int n)
{
    int gap, i, j, temp;
    for (gap = n/2; gap > 0; gap /= 2)
        for (i = gap; i < n; i++)
            for (j = i-gap; j >= 0 && v[j] > v[j+gap]; j -= gap) {
                temp = v[j];
                v[j] = v[j+gap];
                v[j+gap] = temp;
            }
}
```

### Operador vírgula em `for`

Pares de expressões separadas por `,` são avaliadas da esquerda para a direita; tipo e valor são do operando direito:

```c
for (i = 0, j = strlen(s)-1; i < j; i++, j--)
    /* trocar s[i] e s[j] */
```

> [!note] Vírgula como operador vs. separador
> `,` em `for` é operador de sequenciamento. `,` em argumentos de função, declarações, etc. é separador — não garante ordem de avaliação.

**Preferir `for` quando** inicialização, condição e incremento são logicamente relacionados — mantém controle do loop em um lugar.

---

## `do-while`

```c
do
    instrução
while (expressão);
```

Testa condição **no fundo** — o corpo executa **ao menos uma vez**.

Uso menos comum que `while` e `for`. Útil quando o corpo deve executar antes do primeiro teste:

```c
/* itoa: converte inteiro n para string s */
void itoa(int n, char s[])
{
    int i, sign;
    if ((sign = n) < 0)
        n = -n;
    i = 0;
    do {
        s[i++] = n % 10 + '0';
    } while ((n /= 10) > 0);
    if (sign < 0)
        s[i++] = '-';
    s[i] = '\0';
    reverse(s);
}
```

Aqui `do-while` é necessário: ao menos um dígito deve ser gerado, mesmo para `n == 0`.

---

## `break`

Saída imediata do `for`, `while`, `do-while` ou `switch` mais interno:

```c
/* trim: remove espaços/tabs/newlines do fim */
int trim(char s[])
{
    int n;
    for (n = strlen(s)-1; n >= 0; n--)
        if (s[n] != ' ' && s[n] != '\t' && s[n] != '\n')
            break;
    s[n+1] = '\0';
    return n;
}
```

---

## `continue`

Inicia a próxima iteração do loop mais interno (`for`, `while`, `do-while`):

- Em `while`/`do-while`: executa o teste imediatamente
- Em `for`: passa para `expr3` (incremento)
- **Não se aplica a `switch`** — `continue` dentro de `switch` dentro de loop atinge o loop, não o switch

```c
for (i = 0; i < n; i++)
    if (a[i] < 0)
        continue;    /* pula negativos */
    /* ... processa positivos */
```

---

## `goto` e labels

`goto` provê desvio incondicional para um label na mesma função:

```c
for ( ... )
    for ( ... ) {
        if (disaster)
            goto error;
    }
error:
    /* limpa o estado */
```

**Uso legítimo**: sair de loops profundamente aninhados (onde `break` só sai do loop mais interno).

> [!warning] Usar raramente
> `goto` torna código mais difícil de entender e manter. Alternativa sem goto (com flag):
> ```c
> found = 0;
> for (i = 0; i < n && !found; i++)
>     for (j = 0; j < m && !found; j++)
>         if (a[i] == b[j])
>             found = 1;
> ```
> O K&R não usa `goto` no livro. Preferir `break`/`return`/flag quando possível.

---

## Resumo comparativo

| Construção | Teste | Corpo executa ao menos | Saída antecipada |
|---|---|---|---|
| `while` | Topo | 0 vezes | `break` |
| `for` | Topo | 0 vezes | `break` |
| `do-while` | Fundo | 1 vez | `break` |
| `switch` | — (salto) | — | `break` |

---

## Ver também
- [[Linguagem C]] — overview de controle de fluxo; `for` vs. `while`; atribuição como expressão
- [[Operadores C]] — precedência; `&&`/`||` com short-circuit; operador vírgula
- [[Funções em C]] — `return` como saída de função
- [[Arrays em C]] — loops sobre arrays
