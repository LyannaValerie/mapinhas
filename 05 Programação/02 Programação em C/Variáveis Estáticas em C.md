---
title: Variáveis Estáticas em C
aliases:
  - static C
  - variável estática C
  - static local C
  - static externo C
  - static função C
tags:
  - computação/fundamentos
date: 2026-04-21
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Programação]]

# Variáveis Estáticas em C

## Definição

`static` controla **escopo** (visibilidade) e **duração** (tempo de vida). Dois usos distintos: aplicado a variáveis/funções externas, restringe escopo ao arquivo; aplicado a variáveis internas, retém valor entre chamadas.

## Relações (SPO)
- Variável `static` externa → visível somente em → arquivo onde é declarada
- Variável `static` interna → retém valor entre → chamadas da função
- Função `static` → invisível fora de → seu arquivo-fonte
- `static` externo → substitui → variável compartilhada desnecessária entre arquivos

---

## static externo

Aplicado a variável ou função fora de qualquer função: limita a visibilidade ao restante do arquivo-fonte.

```c
static char buf[BUFSIZE];   /* privado ao arquivo */
static int bufp = 0;

int getch(void) { ... }
void ungetch(int c) { ... }
```

`buf` e `bufp` são compartilhados por `getch`/`ungetch` dentro do arquivo mas invisíveis para qualquer outra parte do programa. Sem `static`, seriam globais — qualquer arquivo poderia acessá-los e causar conflitos de nome.

Funções também podem ser `static`:

```c
static int helper(int x) { ... }   /* invisível fora do arquivo */
```

---

## static interno

Aplicado dentro de uma função: a variável é local à função mas **não é destruída ao sair** — retém valor entre chamadas.

```c
/* Exemplo: contador que persiste */
void count_calls(void)
{
    static int n = 0;   /* inicializado uma vez, persiste */
    n++;
    printf("chamada %d\n", n);
}
```

Diferença com automáticas:

| Variável | Criada | Destruída | Retém valor |
|---|---|---|---|
| Automática | Na entrada da função | Na saída | Não |
| `static` interna | Na 1ª chamada | Nunca | Sim |

Inicialização de `static` interno acontece uma única vez, antes da primeira chamada.

> [!tip] Uso prático
> `static` interno é útil para evitar `ungetch` quando a função precisa "lembrar" um caractere entre chamadas (ver Exercício 4-11 do K&R).

---

## Inicialização padrão

Variáveis `static` (internas e externas) são inicializadas a zero por padrão — igual às externas, diferente das automáticas.

```c
static int contador;   /* garantido = 0 */
```

---

## Ver também
- [[Escopo em C]] — variáveis automáticas, externas, `extern`
- [[Funções em C]] — funções em múltiplos arquivos
- [[Inicialização em C]] — regras de inicialização por classe de armazenamento
- [[Pré-processador C]] — `#define`, arquivos separados
