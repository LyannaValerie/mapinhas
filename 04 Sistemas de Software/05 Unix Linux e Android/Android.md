---
title: Android
aliases:
  - sistema Android
  - arquitetura Android
  - aplicações Android
tags:
  - computação/sistemas
source: "Sistemas Operacionais Modernos, cap. 10"
created_by: Codex
---

# Android

## Definição

Plataforma de sistema operacional móvel baseada no núcleo Linux, com runtime, bibliotecas, framework de aplicações e modelo próprio de componentes.

## Papel técnico

Android usa Linux para processos, memória, drivers, rede e segurança, mas expõe ao desenvolvedor uma pilha acima do kernel. Aplicações são organizadas em componentes, comunicam-se por intentos e IPC, e executam isoladas por identidade de aplicação.

## Relações (SPO)

- Android -> usa -> núcleo Linux
- Aplicação Android -> executa em -> caixa de areia (sandbox)
- Intento -> descreve -> operação desejada
- [[Binder]] -> implementa -> IPC central do Android
- [[Dalvik]] -> executa -> bytecode de aplicações Android
- Framework Android -> oferece -> serviços de alto nível
- Zygote -> é pai de -> todos os processos de aplicação (via fork)
- AOSP -> é -> parte de código aberto do Android

## Camadas da Plataforma

```
┌─────────────────────────────────────┐
│   Aplicações (Java/Kotlin)          │
├─────────────────────────────────────┤
│   Framework de Aplicações           │
│   (Activity Manager, Window Mgr...) │
├─────────────────────────────────────┤
│   Runtime (ART) + Bibliotecas       │
│   nativas (C/C++)                   │
├─────────────────────────────────────┤
│   Núcleo Linux + drivers            │
└─────────────────────────────────────┘
```

## Google e AOSP

- **AOSP** (*Android Open Source Project*): código aberto, livre para uso e modificação
- **CDD** (*Compatibility Definition Document*): define comportamentos mínimos para ser "dispositivo Android compatível"
- Serviços proprietários do Google (Play Store, Maps, etc.) exigem compatibilidade CDD como pré-condição

## Objetivos do Projeto

1. Dar suporte a ambiente rico de aplicações de terceiros
2. Ser neutro para funcionalidades de nível superior (acesso à nuvem, APIs de mensagem)
3. Fornecer modelo de segurança onde usuários não precisam confiar profundamente em apps de terceiros
4. Lançar aplicações rapidamente (meta: 200 ms)

## Zygote — Processo Pai

Problema: inicializar o ambiente Java/Dalvik para cada app leva ~1 segundo. Meta de 200 ms fica inatingível.

**Solução**: processo `zygote` inicializa Dalvik e pré-carrega classes/recursos comuns. Ao iniciar um novo app, o sistema faz **fork do Zygote**. O filho obtém cópia-na-escrita de todas as classes pré-carregadas gratuitamente.

```
Zygote → fork → Processo do aplicativo
         CoW    Classes pré-carregadas
```

## Isolamento de Aplicações (Sandbox)

Cada aplicação recebe um **UID Linux único**. O isolamento é garantido pelo mecanismo de processos do Linux:
- Isolamento de memória (espaços de endereçamento separados)
- Limpeza automática de recursos ao terminar o processo
- Segurança baseada em UID (permissões de arquivo e chamadas de sistema)

O Android NÃO usa SecurityManager do Java para isolamento — usa isolamento de processos do Linux.

## Componentes de Aplicação

- **Activity**: tela com interface de usuário
- **Service**: execução em segundo plano sem UI
- **BroadcastReceiver**: responde a eventos do sistema ou de outras apps
- **ContentProvider**: expõe dados para outras aplicações

## Intento (Intent)

Mecanismo de descoberta e ativação de componentes. Ver [[Intento Android]].

## Energia — Wakelocks

Diferença crítica do Linux desktop: o Android assume que o dispositivo deve **dormir por padrão**. Wakelocks impedem o sistema de dormir enquanto há processamento necessário. Ao liberar todos os wakelocks, o sistema volta a dormir.

## OOM Killer no Android

Android adapta o OOM Killer do Linux para prioridade de apps: apps em foreground têm prioridade máxima; apps em background têm prioridade de terminação maior. O nível de "maldade" é configurado pelo framework Android.

## Ver também

- [[Binder]]
- [[Dalvik]]
- [[Segurança no Linux]]
- [[Processos no Linux]]
- [[História do UNIX e do Linux]]
