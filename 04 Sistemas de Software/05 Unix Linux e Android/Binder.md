---
title: Binder
aliases:
  - IPC Binder
  - Android Binder
  - comunicação entre processos Android
tags:
  - computação/sistemas
source: "Sistemas Operacionais Modernos, cap. 10"
created_by: Codex
---

# Binder

## Definição

Mecanismo de comunicação entre processos (IPC) usado pelo Android para chamadas entre aplicações e serviços do sistema, baseado em objetos remotos e RPC.

## Papel técnico

Binder fornece IPC orientado a objetos: um processo chama uma interface, o runtime empacota argumentos, o driver Binder (no núcleo) transporta a transação e o processo servidor executa o método correspondente.

O Android NÃO usa pipes, System V semáforos, memória compartilhada ou filas de mensagens — usa exclusivamente o Binder para a maior parte da comunicação interprocesso.

## Relações (SPO)

- Binder -> implementa -> IPC no [[Android]]
- Cliente Binder -> chama -> objeto remoto via handle
- Driver Binder -> transporta -> transação entre processos
- Serviço Android -> expõe -> interface Binder
- Objeto Binder -> é uma -> capacidade (capability)
- Núcleo -> reescreve -> endereços locais para handles no processo receptor

## Arquitetura em 3 Camadas

```
┌─────────────────────────────────────────┐
│  Plataforma/Aplicação (Java/C++)        │
│  Interface AIDL — declara interfaces    │
├─────────────────────────────────────────┤
│  API de espaço do usuário               │
│  IBinder / Binder                       │
│  transact() / onTransact()              │
├─────────────────────────────────────────┤
│  Módulo de núcleo do Binder             │
│  ioctl() — transporta transações        │
└─────────────────────────────────────────┘
```

- **Módulo de núcleo**: implementa IPC real; exposto via `ioctl`
- **IBinder/Binder**: API básica de espaço do usuário orientada a objetos
- **AIDL**: linguagem de definição de interface; gera código de proxy/stub automaticamente

## Modelo de Transação

Cada comunicação Binder é uma **transação RPC** completa: cliente envia operação + dados → servidor executa → retorna resultado. O driver Binder garante entrega e identidade.

### Passagem de Objetos entre Processos

Processo 1 envia um objeto local para Processo 2:
1. Processo 1 cria transação com endereço local do objeto
2. Submete ao núcleo
3. Núcleo encontra o endereço → cria entrada no mapa de objetos Binder
4. Núcleo reescreve a transação: substitui endereço local por **handle** válido para Processo 2
5. Transação entregue ao Processo 2

Se o mesmo objeto for enviado múltiplas vezes ao mesmo processo, o Binder mantém **identidade única**: sempre o mesmo handle. Diferente de descritores de arquivo Linux, onde abrir o mesmo arquivo gera descritores diferentes.

## Modelo de Segurança

Cada objeto Binder é uma **capacidade** (*capability*):
- Ter a referência ao objeto = ter permissão de usá-lo
- Enviar objeto para outro processo concede essa capacidade
- O núcleo inclui a identidade do processo chamador na transação → controle de permissões baseado em PID/UID

## Ver também

- [[Android]]
- [[Dalvik]]
- [[Processos no Linux]]
- [[Segurança no Linux]]
- [[Chamada do Sistema]]
