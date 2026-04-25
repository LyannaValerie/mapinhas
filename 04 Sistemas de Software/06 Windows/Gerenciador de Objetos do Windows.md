---
title: Gerenciador de Objetos do Windows
aliases:
  - object manager
  - objetos do Windows
  - descritores do Windows
  - handles do Windows
tags:
  - computação/sistemas
  - sistemas-operacionais/windows
source: "Sistemas Operacionais Modernos, cap. 11"
created_by: Codex
---

# Gerenciador de Objetos do Windows

## Definição

Componente da camada executiva NT que administra objetos do modo núcleo: cria, nomeia, controla ciclo de vida e aplica verificações de segurança.

## Tipos de objetos do núcleo

Exemplos de tipos mantidos pelo gerenciador de objetos:

- Processo, thread, job
- Arquivo, diretório de arquivo
- Seção (section) — memória mapeada
- Evento, semáforo, mutex, temporizador
- Chave do registro
- Token de segurança
- Porta de comunicação local (LPC/ALPC)
- Driver, dispositivo

Cada tipo tem um objeto-tipo que define operações permitidas e callbacks do ciclo de vida.

## Estrutura de um objeto

Cada objeto possui:
- **Cabeçalho** (gerenciado pelo gerenciador de objetos): tipo, contagem de referências, nome opcional, informações de segurança
- **Corpo** (específico do tipo): dados internos do objeto

## Namespace do NT

O namespace do NT é uma árvore hierárquica de diretórios de objetos:

```
\
├── Device\           dispositivos (discos, etc.)
├── KernelObjects\    objetos globais do núcleo
├── ObjectTypes\      tipos de objeto registrados
├── BaseNamedObjects\ objetos nomeados de modo usuário
└── Sessions\         namespaces por sessão de login
```

## Descritores (handles)

Cada processo mantém uma **tabela de descritores** privada. Um descritor é um inteiro que referencia um objeto no núcleo sem expor seu endereço. Descritores podem ser:
- Herdáveis por processos filhos
- Duplicados entre processos via `DuplicateHandle`

O gerenciador de objetos verifica segurança ao criar um descritor: se o token do chamador não tem os direitos pedidos, a abertura é negada.

## Observação

Objeto no Windows não implica herança ou polimorfismo — há abstração e ocultação de dados, mas o mecanismo é implementado em C com tabelas de funções por tipo.

## Relações (SPO)

- Gerenciador de Objetos do Windows -> cria -> descritores
- Gerenciador de Objetos do Windows -> mantém -> namespace do NT
- Descritor -> referencia -> objeto do modo núcleo
- [[Segurança no Windows 8]] -> valida -> acesso a objetos
- [[Processos e Threads no Windows]] -> usam -> descritores

