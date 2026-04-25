---
title: Cache do Windows
aliases:
  - gerenciador de cache do Windows
  - cache manager
  - views de cache
tags:
  - computação/sistemas
  - sistemas-operacionais/windows
  - computação/armazenamento
source: "Sistemas Operacionais Modernos, cap. 11"
created_by: Codex
---

# Cache do Windows

## Definição

Mecanismo que aumenta desempenho de sistemas de arquivos mantendo em memória regiões recentes ou frequentemente acessadas de arquivos.

## Modelo: cache por visões

Em vez de armazenar blocos físicos, o gerenciador de cache mantém **visões** (*views*): regiões de arquivos mapeadas no espaço de endereçamento virtual do núcleo. Cada visão tem ~256 KB por padrão.

Esse modelo se integra naturalmente ao NTFS, que armazena todos os seus metadados como arquivos — metadados e dados de usuário passam pelo mesmo mecanismo de cache.

## Separação de responsabilidades

| Componente | Responsabilidade |
|------------|-----------------|
| Gerenciador de cache | administra visões, coordena leitura antecipada e escrita adiada |
| [[Gerenciamento de Memória no Windows\|Gerenciador de memória]] | gerencia páginas físicas por trás das visões |

Quando um acesso a um arquivo em cache gera falta de página, o gerenciador de memória a satisfaz lendo do arquivo via I/O.

## Comportamentos principais

- **Leitura antecipada** (*read-ahead*): o gerenciador de cache detecta padrão sequencial e busca dados antes da solicitação.
- **Escrita adiada** (*write-behind*): modificações nas visões são propagadas ao disco de forma assíncrona.
- **Cache compartilhado**: todos os sistemas de arquivos montados no sistema usam o mesmo gerenciador de cache.

## Relações (SPO)

- Cache do Windows -> acelera -> [[Sistema de Arquivos do Windows NT]]
- Cache do Windows -> usa -> visões de arquivo no espaço do núcleo
- [[Gerenciamento de Memória no Windows]] -> gerencia -> páginas físicas do cache
- NTFS -> armazena -> metadados como arquivos (incluídos no cache)

