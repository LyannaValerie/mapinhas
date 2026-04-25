---
title: Segurança no Linux
aliases:
  - permissões Linux
  - segurança UNIX
  - UID
  - GID
  - setuid
tags:
  - computação/sistemas
source: "Sistemas Operacionais Modernos, cap. 10"
created_by: Codex
---

# Segurança no Linux

## Definição

Modelo de proteção do Linux baseado em usuários, grupos, permissões de objetos, credenciais de processos e checagens feitas pelo núcleo. Inserido desde o início como sistema multiusuário.

## Papel técnico

Linux aplica segurança no limite entre processo e recurso. Cada processo executa com credenciais; cada arquivo, diretório ou objeto do núcleo tem regras de acesso. O núcleo autoriza ou nega operações antes de expor dados ou alterar estado.

## Relações (SPO)

- Usuário -> identificado por -> UID (inteiro 0–65535)
- Grupo -> identificado por -> GID
- Processo -> carrega -> UID e GID efetivos
- Arquivo -> possui -> dono (UID), grupo (GID) e 9 bits de permissão
- `setuid` -> permite -> execução com privilégios do dono do arquivo
- Root (UID 0) -> ignora -> verificações de permissão
- Núcleo Linux -> valida -> acesso a recursos

## Modelo de Permissões

Cada arquivo tem 9 bits de proteção: **rwx** para dono, grupo e outros.

```
rwxrwxrwx
│││││││└┘ outros (world)
│││└┘└─── grupo
└┘──────── dono
```

| Binário | Simbólico | Semântica |
|---------|-----------|-----------|
| `111000000` | `rwx------` | Dono: lê, escreve, executa; ninguém mais |
| `111111000` | `rwxrwx---` | Dono e grupo: lê, escreve, executa |
| `110100000` | `rw-r-----` | Dono: lê e escreve; grupo: só lê |
| `110100100` | `rw-r--r--` | Dono: lê e escreve; todos os outros: só lêem |
| `000000000` | `---------` | Nenhum acesso (exclusão mútua de existência) |

Para diretórios: bit `x` = permissão de **travessia** (busca), não execução.

## Root (Superusuário)

**UID 0** = superusuário (root):
- Lê e escreve qualquer arquivo, independente de permissões
- Pode executar chamadas de sistema negadas a usuários comuns
- Normalmente: apenas o administrador conhece a senha

## SETUID

Problema: como permitir que um usuário comum execute ação que requer privilégio (ex: imprimir)?

**Solução**: bit SETUID no arquivo executável. Quando processo executa um programa com SETUID ativado, o processo assume o **UID do dono do arquivo** (não do usuário que executou).

```sh
chmod u+s /usr/bin/passwd   # passwd roda com UID do root
```

Isso permite controle de acesso a recursos restritos (impressora, base de senhas) sem dar root ao usuário.

## Dispositivos como Arquivos

Arquivos especiais em `/dev` têm os mesmos bits de proteção. Exemplo:
- `/dev/lp` (impressora) com modo `rw-------` pertencendo a `daemon` → usuários comuns não podem imprimir diretamente → precisam usar serviço com SETUID

## Chamadas de Sistema para Segurança

| Chamada | Descrição |
|---------|-----------|
| `s = chmod(name, mode)` | Muda bits de proteção do arquivo |
| `s = chown(name, owner, group)` | Muda dono/grupo |
| `uid = getuid()` | Retorna UID real do processo |
| `uid = geteuid()` | Retorna UID efetivo |
| `s = setuid(uid)` | Define UID efetivo (requer root) |

## Segurança no Android

Android estende o modelo UID do Linux para isolamento de aplicações:
- Cada app recebe UID único na instalação
- Processos separados por UID = isolamento garantido pelo Linux
- Substitui SecurityManager Java pelo isolamento de processos do SO

## Ver também

- [[Sistema de Arquivos Linux]]
- [[Processos no Linux]]
- [[Espaço de Usuário e Espaço de Kernel]]
- [[Android]]
