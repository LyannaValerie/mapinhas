---
title: Chamadas de Sistema POSIX
aliases:
  - syscalls POSIX
  - chamadas POSIX
  - interface POSIX
tags:
  - computação/sistemas
source: "Sistemas Operacionais Modernos, cap. 1"
created_by: Codex
date: 2026-04-25
---

%% criado pelo Codex %%

# Chamadas de Sistema POSIX

## Definição

Conjunto padronizado de interfaces de [[Chamada do Sistema]] e funções associadas, definido pelo IEEE para garantir portabilidade entre sistemas compatíveis com UNIX. O nome vem de *Portable Operating System Interface*.

POSIX especifica uma interface mínima: sistemas em conformidade devem implementar as chamadas definidas, mas podem oferecer extensões adicionais.

## Mecanismo de chamada

Uma chamada POSIX como `read(fd, buf, n)` envolve (Tanenbaum §1.6, [[Modo Kernel e Modo Usuário]]):

1. Programa chama wrapper de biblioteca (e.g., `libc`)
2. Wrapper coloca o número da syscall em registrador (e.g., `rax = 0` para `read` em Linux x86-64)
3. Wrapper executa instrução `syscall` (TRAP para o kernel)
4. CPU troca para modo kernel; salta para handler
5. Kernel verifica parâmetros, executa a operação
6. Resultado retorna em registrador; modo usuário restaurado
7. Wrapper propaga valor de retorno ou seta `errno`

## Grupos funcionais (Tanenbaum §1.6)

| Grupo | Chamadas centrais | Efeito |
|---|---|---|
| Processos | `fork`, `execve`, `wait`, `exit`, `getpid` | Criar, substituir, sincronizar, encerrar |
| Sinais | `signal`, `kill`, `alarm`, `pause` | Comunicação assíncrona entre processos |
| Arquivos | `open`, `read`, `write`, `close`, `lseek`, `stat` | Operações sobre descritores |
| Diretórios | `mkdir`, `rmdir`, `link`, `unlink`, `rename`, `mount` | Estrutura do sistema de arquivos |
| Proteção | `chmod`, `chown`, `umask`, `getuid`, `setuid` | Permissões e identidade |
| Tempo | `time`, `utime`, `clock_gettime` | Consultar e ajustar tempo |

## Retornos e erros

Chamadas POSIX retornam `-1` em caso de erro e setam `errno` com o código do problema. `perror()` converte `errno` em mensagem legível.

## Papel técnico

POSIX desacopla o programa do kernel concreto. O mesmo código que usa `open`/`read`/`write` funciona em Linux, macOS, FreeBSD e qualquer sistema certificado POSIX. Drivers e detalhes internos ficam ocultos atrás dessa interface.

## Relações (SPO)

- POSIX → padroniza → interface de [[Chamada do Sistema]] compatível com UNIX
- Chamadas POSIX → controlam → processos, arquivos, diretórios, proteção e tempo
- Wrapper de biblioteca → traduz → chamada C para instrução TRAP do kernel
- [[Modo Kernel e Modo Usuário]] → implementa → transição de modo para syscall
- `fork` + `exec` → implementam → [[Hierarquia de Processos]] em UNIX
- `read`/`write` → operam sobre → descritores de arquivo e [[Pipe|pipes]]
- MINIX 3 → implementa → interface POSIX compatível com UNIX
