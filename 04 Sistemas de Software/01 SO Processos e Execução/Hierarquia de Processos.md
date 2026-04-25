---
title: Hierarquia de Processos
aliases:
  - process hierarchy
  - árvore de processos
  - processo pai
  - processo filho
tags:
  - computação/sistemas
source: "Sistemas Operacionais Modernos, cap. 1"
created_by: Codex
date: 2026-04-25
---

%% criado pelo Codex %%

# Hierarquia de Processos

## Definição

Organização de processos em relações de criação, na qual um processo pai cria processos filhos, formando uma árvore com raiz no processo inicial do sistema (Tanenbaum §1.5.1).

## Criação no UNIX: fork + exec

UNIX separa criação e substituição de programa em duas syscalls distintas:

| Chamada | Efeito |
|---|---|
| `fork()` | Cria cópia quase idêntica do processo chamador (pai e filho continuam do mesmo ponto) |
| `exec(prog, args)` | Substitui a imagem de programa do processo atual por `prog`; não cria novo processo |
| `wait()` | Pai bloqueia até que um filho termine; recebe status de saída |
| `exit(status)` | Encerra o processo e retorna `status` ao pai via `wait` |

O retorno de `fork()` é 0 no filho e o PID do filho no pai, permitindo ao código distinguir os dois casos.

## Raiz da hierarquia: init

Em sistemas UNIX, ao inicializar, o kernel cria manualmente o **processo init** com PID 1. Todos os outros processos do sistema são descendentes de init. Quando um processo pai termina antes do filho, o filho é "adotado" por init (que chama `wait` para recolhê-los).

```
init (PID 1)
├── getty (terminal login)
│   └── shell
│       ├── comando1
│       └── comando2
└── daemon
```

## Grupos de processos e sinais

Processos UNIX são organizados em **grupos de processos**. Sinais (como `SIGINT` do Ctrl+C no terminal) são enviados a todos os processos do grupo de primeiro plano de uma vez. Isso permite que um `kill` atinja um pipeline inteiro de processos.

## Diferença: Windows

No Windows, não há `fork`. Processos são criados com `CreateProcess()`, que carrega um novo executável diretamente. Não há hierarquia de processos no mesmo sentido — pai e filho são independentes desde a criação.

## Relações (SPO)

- Processo pai → cria → processo filho via `fork()`
- `fork()` → retorna → PID do filho ao pai e 0 ao filho
- `exec()` → substitui → imagem de programa sem criar novo processo
- `wait()` → sincroniza → pai com término do filho
- init (PID 1) → é → raiz da hierarquia de processos UNIX
- Processo órfão → é adotado por → init
- Grupo de processos → recebe → sinais enviados ao terminal
- [[Shell]] → usa → fork + exec para executar comandos
- [[Tabela de Processos]] → armazena → PID, PPID (pai) e estado de cada processo
