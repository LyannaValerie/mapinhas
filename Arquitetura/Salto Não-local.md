---
title: Salto Não-local
aliases:
  - nonlocal jump
  - salto não-local
  - setjmp
  - longjmp
  - sigsetjmp
  - siglongjmp
tags:
  - computação/arquitetura
date: 2026-04-14
---

# Salto Não-local

## Definição
Mecanismo de [[Fluxo de Controle Excepcional|ECF]] em nível de programa que transfere controle **diretamente de uma função para outra já em execução**, sem seguir a disciplina normal de call/return (sem desenrolar a pilha frame a frame).

Em C, provido por `setjmp`/`longjmp`. É a base de baixo nível para `try/catch/throw` em C++ e Java.

## Relações (SPO)
- `setjmp` → salva → ambiente de chamada (env buffer)
- `longjmp` → restaura → ambiente salvo por `setjmp`
- Salto não-local → viola → disciplina de pilha call/return
- `sigsetjmp`/`siglongjmp` → versão segura para → uso dentro de signal handlers

---

## API

### `setjmp` — salvar ambiente

```c
#include <setjmp.h>
int setjmp(jmp_buf env);
int sigsetjmp(sigjmp_buf env, int savesigs);
// Retorna: 0 na chamada direta; valor não-zero quando retornado por longjmp
```

`setjmp` salva o **ambiente de chamada** atual no buffer `env`:
- PC (program counter)
- Stack pointer
- Registradores de uso geral

> [!warning] Não atribuir retorno de setjmp a variável
> `rc = setjmp(env);` é **incorreto** — o valor de retorno não deve ser atribuído. Usar diretamente como condição em `switch` ou `if`.

### `longjmp` — restaurar e pular

```c
#include <setjmp.h>
void longjmp(jmp_buf env, int retval);
void siglongjmp(sigjmp_buf env, int retval);
// Nunca retorna
```

`longjmp` restaura o ambiente salvo em `env` e dispara um **retorno do `setjmp` correspondente** com valor `retval` (nunca zero — se passar 0, retorna 1).

---

## Semântica: chamadas e retornos assimétricos

| Função | Chamada | Retornos |
|---|---|---|
| `setjmp` | 1 vez | Múltiplas vezes (1 direta + 1 por cada `longjmp`) |
| `longjmp` | 1 vez | Nunca retorna |

```
main() chama setjmp → retorna 0 (salva env)
foo() detecta erro → chama longjmp(env, 1)
setjmp retorna 1 (aparece como se fosse novo retorno)
main() trata o erro
```

---

## Uso: recuperação de erro em chamadas aninhadas

Sem salto não-local, um erro em função profundamente aninhada exige propagação manual (checar retorno em cada nível). Com `setjmp`/`longjmp`:

```c
#include <setjmp.h>
#include <stdio.h>

jmp_buf env;

void nivel3(void) {
    // erro detectado fundo na pilha
    longjmp(env, 1);  // pula direto para o setjmp em main
}

void nivel2(void) { nivel3(); }
void nivel1(void) { nivel2(); }

int main(void) {
    switch (setjmp(env)) {
        case 0:
            nivel1();   // execução normal
            break;
        case 1:
            printf("Erro recuperado\n");
            break;
    }
    return 0;
}
```

---

## Uso em signal handlers — `sigsetjmp`/`siglongjmp`

Dentro de signal handlers, usar `sigsetjmp`/`siglongjmp` (com `savesigs = 1` para salvar/restaurar a signal mask):

```c
sigjmp_buf env;

void handler(int sig) {
    siglongjmp(env, 1);  // pula de volta para o ponto de espera
}

int main(void) {
    signal(SIGINT, handler);
    if (sigsetjmp(env, 1) == 0) {
        // loop de espera
        while (1) pause();
    }
    printf("SIGINT recebido — saindo\n");
    return 0;
}
```

---

## Limitações

- Só pode pular **para cima na pilha** (para função ancestral, não filha)
- Variáveis locais da função que chamou `setjmp` devem ser `volatile` se modificadas entre `setjmp` e `longjmp` — compilador pode otimizá-las em registradores que `longjmp` não restaura
- `longjmp` não chama destrutores de objetos (C++) — para isso usar `throw`/`catch`

---

## Ver também
- [[Fluxo de Controle Excepcional]] — salto não-local como ECF em nível programa
- [[Sinais]] — `sigsetjmp`/`siglongjmp` para handlers; signal mask
- [[Arquitetura/Pilha]] — disciplina de pilha que o salto não-local viola
- [[Linguagem C]] — contexto de uso em C
