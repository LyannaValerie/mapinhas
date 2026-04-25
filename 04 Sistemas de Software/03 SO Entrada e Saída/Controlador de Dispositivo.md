---
title: Controlador de Dispositivo
aliases:
  - controlador de E/S
  - device controller
tags:
  - computação/sistemas
source: "Sistemas Operacionais Modernos, cap. 5"
created_by: Codex
---

# Controlador de Dispositivo

Conteúdo criado pelo Codex.

## Definição

**Controlador de dispositivo** é o componente de hardware que recebe comandos do sistema operacional e controla diretamente um dispositivo físico.

## Interface com CPU

O controlador expõe registradores de controle, registradores de estado e, em muitos casos, buffer de dados. A CPU ou o [[DMA]] usa essa interface para iniciar operações, consultar estado e transferir dados.

## Relações (SPO)

- Controlador de Dispositivo -> controla -> dispositivo físico
- [[Driver]] -> programa -> Controlador de Dispositivo
- Controlador de Dispositivo -> expõe -> registradores de dispositivo
- Controlador de Dispositivo -> pode gerar -> [[Interrupção]]
- Controlador de Dispositivo -> pode cooperar com -> [[DMA]]

