---
title: Modelo Cliente-Servidor em Sistemas Operacionais
aliases:
  - client-server model em SO
  - modelo cliente-servidor em SO
  - servidores de sistema operacional
tags:
  - computação/sistemas
source: "Sistemas Operacionais Modernos, cap. 1"
created_by: Codex
date: 2026-04-25
---

%% criado pelo Codex %%

# Modelo Cliente-Servidor em Sistemas Operacionais

## Definição

Estrutura em que serviços do [[Sistema Operacional]] são executados por processos servidores separados, e clientes (programas de aplicação ou outros servidores) solicitam operações por troca de mensagens (Tanenbaum §1.7.4). Geralmente implementado sobre um [[Micronúcleo]].

## Mecanismo

```
Aplicação (cliente)
    │ envia mensagem de requisição
    ▼
[ Micronúcleo: IPC mínimo ]
    │ entrega mensagem
    ▼
Servidor de sistema de arquivos (processo de usuário)
    │ processa requisição
    │ envia mensagem de resposta
    ▼
[ Micronúcleo: IPC ]
    │
    ▼
Aplicação (recebe resultado)
```

O micronúcleo fornece apenas a primitiva de transporte de mensagens. Toda a lógica do serviço fica no servidor — em espaço de usuário, sem privilégios.

## Serviços típicos implementados como servidores

| Servidor | Responsabilidade |
|---|---|
| Servidor de arquivos | Operações de leitura, escrita, abertura, diretórios |
| Servidor de memória | Gerenciamento de memória virtual |
| Servidor de processos | Criação, escalonamento, sinais |
| Servidor de rede | Pilha TCP/IP, sockets |
| Driver de dispositivo | Controle de hardware específico |

## Vantagem de isolamento

Um servidor defeituoso pode ser reiniciado sem derrubar o sistema inteiro. Em um kernel monolítico, um bug em qualquer driver ou componente pode corromper todo o espaço de endereçamento do kernel. No modelo cliente-servidor, o servidor é apenas um processo — o kernel pode terminá-lo e relançá-lo (como o MINIX 3 faz com drivers).

## Extensão para sistemas distribuídos

O modelo se generaliza naturalmente para redes: o cliente não precisa saber se o servidor está na mesma máquina ou em outra. A passagem de mensagens é o mecanismo uniforme, e chamadas de procedimento remoto (RPC) estendem o modelo para comunicação em rede.

## Trade-off

| Aspecto | Benefício | Custo |
|---|---|---|
| Isolamento | Falha no servidor não derruba o kernel | — |
| Modularidade | Servidores substituíveis independentemente | Mais processos para gerenciar |
| Desempenho | — | Troca de mensagens > chamada direta interna |

## Relações (SPO)

- Cliente → envia → mensagem de requisição ao servidor
- Servidor → executa → serviço em espaço de usuário
- [[Micronúcleo]] → fornece → IPC mínimo para transporte de mensagens
- Modelo cliente-servidor → reduz → código executando em [[Modo Kernel e Modo Usuário|modo kernel]]
- Servidor defeituoso → pode ser reiniciado → sem reiniciar o sistema
- Modelo cliente-servidor → generaliza para → sistemas distribuídos via RPC
- [[Estrutura Monolítica de Sistema Operacional]] → contrasta com → modelo cliente-servidor
