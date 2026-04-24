---
title: Arquivo
aliases:
  - file
  - abstração de arquivo
  - tudo é arquivo
  - everything is a file
tags:
  - computação/arquitetura
  - computação/sistemas-operacionais
date: 2026-04-18
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Arquivo

## Definição
No contexto de sistemas operacionais Unix/Linux, **arquivo** é a abstração central do [[Sistema Operacional]]: uma **sequência de bytes** — nada mais, nada menos. Todo dispositivo de E/S é modelado como um arquivo, permitindo que o mesmo conjunto de [[Chamada do Sistema|chamadas de sistema]] sirva para disco, teclado, display e rede.

É uma das três abstrações fundamentais do SO sobre o hardware (junto com [[Processo]] e [[Memória Virtual]]).

## Relações (SPO)
- Arquivo → abstrai → dispositivos de E/S (disco, teclado, display, rede, pipes)
- Arquivo → acessado via → [[Descritores de Arquivo UNIX]] (open, read, write, close)
- Arquivo → implementado por → [[Sistema de Arquivos]]
- SO → expõe → todo hardware como arquivo (paradigma Unix)
- Arquivo → é uma das três abstrações de → [[Sistema Operacional]] (com [[Processo]] e [[Memória Virtual]])

## Paradigma "Tudo é Arquivo"

```
Dispositivo     Abstração Unix
────────────    ──────────────
Disco           → arquivo em sistema de arquivos
Teclado         → /dev/stdin  (arquivo de leitura)
Display         → /dev/stdout (arquivo de escrita)
Placa de rede   → socket (arquivo de rede)
Pipe            → arquivo anônimo entre processos
Dispositivos    → /dev/sda, /dev/tty, etc.
```

Essa uniformidade permite que programas funcionem com diferentes dispositivos **sem modificação** — conectados por pipes e redirecionamentos.

## Interface Unix I/O

| Chamada | Operação |
|---|---|
| `open(path, flags)` | Abre/cria arquivo; retorna descritor (fd) |
| `read(fd, buf, n)` | Lê até n bytes do fd |
| `write(fd, buf, n)` | Escreve n bytes no fd |
| `close(fd)` | Libera o descritor |
| `lseek(fd, offset, whence)` | Move posição de leitura/escrita |

Todo processo herda três descritores abertos: `0` (stdin), `1` (stdout), `2` (stderr).

## Relação com Rede
O adaptador de rede conecta-se ao [[Barramento|barramento de E/S]] como qualquer controlador. Conexões de rede são acessadas via **sockets** — descritores de arquivo — pelo mesmo mecanismo de `read`/`write`. Ver [[Sockets]] e [[Rede]].

## Ver também
- [[Sistema Operacional]]
- [[Descritores de Arquivo UNIX]]
- [[Sistema de Arquivos]]
- [[Processo]]
- [[Memória Virtual]]
- [[Sockets]]
- [[Rede]]
- [[Chamada do Sistema]]
