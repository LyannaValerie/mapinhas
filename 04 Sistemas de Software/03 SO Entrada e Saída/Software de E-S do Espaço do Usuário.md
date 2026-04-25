---
title: Software de E/S do Espaço do Usuário
aliases:
  - user-space I/O software
  - software de I/O em user space
tags:
  - computação/sistemas
source: "Sistemas Operacionais Modernos, cap. 5"
created_by: Codex
---

# Software de E/S do Espaço do Usuário

Conteúdo criado pelo Codex.

## Definição

**Software de E/S do espaço do usuário** é a parte do subsistema de E/S implementada fora do kernel, geralmente em bibliotecas, daemons ou utilitários.

## Exemplos

- rotinas de biblioteca que invocam [[Chamada do Sistema]]
- bibliotecas de E/S padrão
- sistemas de spool
- programas de configuração e administração de dispositivos

## Relações (SPO)

- Software de E/S do Espaço do Usuário -> chama -> [[Chamada do Sistema]]
- Software de E/S do Espaço do Usuário -> pode implementar -> [[Spooling]]
- Software de E/S do Espaço do Usuário -> depende de -> [[Kernel]]
- Software de E/S do Espaço do Usuário -> fica acima -> [[Software de E-S Independente de Dispositivo|Software de E/S Independente de Dispositivo]]
