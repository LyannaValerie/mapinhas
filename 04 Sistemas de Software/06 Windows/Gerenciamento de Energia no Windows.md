---
title: Gerenciamento de Energia no Windows
aliases:
  - power manager do Windows
  - hibernação no Windows
tags:
  - computação/sistemas
  - sistemas-operacionais/windows
source: "Sistemas Operacionais Modernos, cap. 11"
created_by: Codex
---

# Gerenciamento de Energia no Windows

## Definição

Subsistema que reduz desperdício de energia em desktops, notebooks e servidores, coordenando estados de dispositivos e estados globais do sistema via ACPI.

## Estados de sistema (ACPI S-states)

| Estado | Nome | Descrição |
|--------|------|-----------|
| S0 | On | Sistema em plena operação |
| S1 | Sleep | CPU e RAM com energia, clock reduzido |
| S2 | Sleep | Similar a S1, CPU sem energia |
| S3 | Suspend to RAM | Memória mantida, restante desligado |
| S4 | Hibernate | Memória salva em disco, sistema desligado |
| S5 | Soft Off | Sistema desligado (sem hibernação) |

## Estados de dispositivo (D-states)

Cada dispositivo ACPI tem estados D0 (on) a D3 (off). O gerenciador de energia envia IRPs de energia aos drivers ao transitar entre estados.

## Hibernação

1. Gerenciador de energia notifica drivers e serviços da transição para S4
2. Conteúdo da memória física é copiado para `hiberfil.sys` no disco
3. Sistema desliga completamente
4. Na próxima inicialização, bootloader detecta hibernate e restaura `hiberfil.sys` para memória
5. Sistema retoma do ponto exato

## Outras otimizações

- Redução dinâmica do clock de CPU em processadores ociosos
- Desligamento de CPUs individuais em multiprocessadores quando não necessárias
- Estados de espera (*standby*) para dispositivos ociosos (disco, tela)
- *Connected standby* no Windows 8: baixo consumo com manutenção de conexão de rede

## Relações (SPO)

- Gerenciamento de Energia no Windows -> controla -> estados de dispositivos (D0–D3)
- Hibernação -> salva -> memória física em `hiberfil.sys`
- Drivers -> participam de -> transições de energia via IRPs
- [[Windows 8]] -> suporta -> connected standby

