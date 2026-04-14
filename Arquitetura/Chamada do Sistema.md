---
title: Chamada do Sistema
aliases:
  - system call
  - chamada do supervisor
  - macro de sistema operacional
  - syscall
tags:
  - computação/arquitetura
date: 2026-04-06
---

# Chamada do Sistema

## Definição
Instrução do nível 3 ([[Sistema Operacional]]) que não existe no nível [[ISA]]. Permite que programas solicitem serviços ao SO — como operações de entrada/saída, alocação de memória e controle de processos.

## Relações (SPO)
- Chamada do Sistema → pertence ao → nível 3 ([[Sistema Operacional]])
- Chamada do Sistema → não existe em → nível [[ISA]] (nível 2)
- Chamada do Sistema → interpretada por → [[Sistema Operacional]]
- Chamada do Sistema → evoluiu de → cartões de controle (FMS, IBM 709, ~1960)

## Evolução do nome

| Época | Nome |
|---|---|
| ~1960 | Instruções virtuais (cartões `*FORTRAN`, `*DATA`) |
| 1960s–1970s | Macros de sistema operacional / chamadas do supervisor |
| Hoje | **Chamada do sistema** (*system call*) |

---

## Syscalls de Unix I/O (CS:APP Cap. 10)

Unix I/O é implementado inteiramente via chamadas de sistema. Todo acesso a arquivo, terminal ou rede passa por syscalls:

| Syscall | Assinatura resumida | Retorno |
|---|---|---|
| `open` | `open(filename, flags, mode)` | fd se OK; -1 em erro |
| `close` | `close(fd)` | 0 se OK; -1 em erro |
| `read` | `read(fd, buf, n)` | bytes lidos; 0 em EOF; -1 em erro |
| `write` | `write(fd, buf, n)` | bytes escritos; -1 em erro |
| `stat` | `stat(filename, &buf)` | 0 se OK; -1 em erro |
| `fstat` | `fstat(fd, &buf)` | 0 se OK; -1 em erro |
| `lseek` | `lseek(fd, offset, whence)` | nova posição; -1 em erro |
| `dup2` | `dup2(oldfd, newfd)` | fd não-negativo; -1 em erro |

Todas retornam -1 em erro e definem `errno`.

`open` sempre retorna o menor fd disponível no processo. Descritores 0/1/2 são stdin/stdout/stderr — presentes em todo processo desde o início.

`dup2(oldfd, newfd)` copia entrada da tabela de descritores — usado para redirecionamento de I/O no [[Shell]] e para redirecionar stdout ao socket do cliente em servidores CGI ([[Sockets]]).

> [!note] Overhead de syscall
> Cada syscall envolve trap ao kernel (mudança de modo usuário → modo supervisor). Por isso o pacote [[Unix I/O|RIO]] e a biblioteca standard I/O usam buffers internos — reduzem o número de traps minimizando chamadas diretas a `read`.

## Ver também
- [[Sistema Operacional]]
- [[Níveis de Abstração]]
- [[ISA]]
- [[Unix I/O]] — implementação de E/S inteiramente via syscalls; RIO; redirecionamento
- [[Sockets]] — sockets são arquivos Unix; lidos/escritos via mesmas syscalls `read`/`write`
- [[Modos de Operação x86]] — trap muda CPU de modo usuário para modo supervisor
