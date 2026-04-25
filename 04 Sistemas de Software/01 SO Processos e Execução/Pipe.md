---
title: Pipe
aliases:
  - pipes
  - canal UNIX
  - pipeline
tags:
  - computação/sistemas
source: "Sistemas Operacionais Modernos, cap. 1"
created_by: Codex
date: 2026-04-25
---

%% criado pelo Codex %%

# Pipe

## Definição

Canal unidirecional de comunicação entre processos, exposto como fluxo de bytes através de descritores de arquivo. Conceituado como um "pseudoarquivo" que conecta dois processos (Tanenbaum §1.5.7).

## Mecanismo

Dois processos que querem usar um pipe precisam criá-lo antes de se comunicar. A criação tipicamente ocorre antes de `fork()`: o pai cria o pipe, faz `fork()`, e cada processo fecha a extremidade que não usa.

```
pipe(fds)       → fds[0] = extremidade de leitura
                  fds[1] = extremidade de escrita
fork()
└── pai fecha fds[0]; escreve em fds[1]
└── filho fecha fds[1]; lê de fds[0]
```

A implementação interna do pipe lembra um arquivo: o processo A escreve com `write()` e o processo B lê com `read()`, sem saber se está lendo de arquivo ou de pipe (exceto via `fstat()`).

## Comportamento do buffer do kernel

- `write()` bloqueia quando o buffer está cheio (pipe cheio, consumidor lento)
- `read()` bloqueia quando o buffer está vazio (pipe vazio, produtor lento)
- `read()` retorna 0 (EOF) quando todos os descritores de escrita foram fechados
- Tamanho típico do buffer: 64 KB em Linux

## Pipes nomeados (FIFOs)

Além de pipes anônimos (que existem apenas na memória), UNIX oferece **pipes nomeados** (FIFOs):

```
mkfifo /tmp/meu_pipe       # cria FIFO no sistema de arquivos
```

FIFOs têm um caminho no sistema de arquivos; processos sem relação de parentesco podem se comunicar abrindo o mesmo FIFO. Um pipe anônimo só existe entre processos relacionados (pai/filho).

## Uso no shell

```bash
cat arquivo | grep padrão | sort | uniq -c
```

O shell cria um processo para cada comando e conecta a saída padrão de cada um à entrada padrão do seguinte via pipe.

## Relações (SPO)

- Pipe → conecta → saída de um [[Processo]] à entrada de outro
- Pipe → é implementado como → pseudoarquivo com buffer no kernel
- `write()` em pipe cheio → bloqueia → processo produtor
- `read()` em pipe vazio → bloqueia → processo consumidor
- `read()` retorna EOF → quando → todos os descritores de escrita estão fechados
- FIFO → é → pipe com nome no sistema de arquivos
- [[Shell]] → usa → pipes para compor comandos
- Pipe → usa → descritores de arquivo (interface uniforme com `read`/`write`)
- [[Chamadas de Sistema POSIX]] → incluem → `pipe()` para criar pipes anônimos
