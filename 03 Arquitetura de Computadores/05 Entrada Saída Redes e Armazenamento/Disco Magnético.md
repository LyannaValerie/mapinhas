---
title: Disco Magnético
aliases:
  - disk storage
  - hard disk
  - HD
  - HDD
  - magnetic disk
  - armazenamento magnético
  - seek time
  - rotational latency
tags:
  - computação/arquitetura
  - sistemas
  - io
date: 2026-04-23
source: "P&H COD Cap. 8 §8.2"
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Disco Magnético

Dispositivo de armazenamento **não volátil** — dados persistem sem energia. Usa pratos (platters) com superfície magnética e cabeças de leitura/escrita móveis.

## Estrutura Física

| Componente | Descrição | Valores típicos |
|---|---|---|
| **Platters** | Pratos rotativos com superfície magnética | 1–4 por disco |
| **Superfícies** | Cada platter tem 2 superfícies graváveis | 2–8 por disco |
| **Trilha (track)** | Círculo concêntrico em uma superfície | 10.000–50.000/superfície |
| **Setor (sector)** | Subdivisão de uma trilha; menor unidade de R/W | 100–500/trilha; 512 B típico |
| **Cilindro (cylinder)** | Conjunto de todas as trilhas na mesma posição radial em todas as superfícies | — |

**Zone Bit Recording (ZBR):** trilhas externas têm mais setores que trilhas internas (densidade de bits constante). Aumenta capacidade total.

## Acesso ao Disco

O SO dirige o disco em 3 etapas sequenciais:

```
T_acesso = T_seek + T_rotacional + T_transferência + T_controlador
```

### T_seek (Tempo de Seek)
Posicionar a cabeça na trilha correta.
- Advertised avg: **3–14 ms**
- Actual avg ≈ **25–33% do advertised** (por causa de localidade de acesso)

### T_rotacional (Latência Rotacional)
Aguardar o setor desejado girar até a cabeça.

$$T_\text{rot\_avg} = \frac{0{,}5\ \text{rotação}}{\text{RPM} / 60}$$

| RPM | T_rot_avg |
|---|---|
| 5.400 | 5,6 ms |
| 7.200 | 4,2 ms |
| 10.000 | 3,0 ms |
| 15.000 | 2,0 ms |

### T_transferência (Transfer Time)
$$T_\text{transf} = \frac{\text{tamanho do setor}}{\text{taxa de transferência}}$$

Exemplo: 512 B a 50 MB/s → 0,01 ms

### T_controlador
Overhead do controlador de disco. Típico: **~0,2 ms**

## Exemplo de Cálculo

Disco a 10.000 RPM, seek avg = 6 ms (advertised), taxa = 50 MB/s, controlador = 0,2 ms, setor = 512 B:

$$T = 6{,}0 + 3{,}0 + 0{,}01 + 0{,}2 = 9{,}2\ \text{ms (advertised)}$$

Com seek real ≈ 25%:

$$T = 1{,}5 + 3{,}0 + 0{,}01 + 0{,}2 = 4{,}7\ \text{ms}$$

> [!note] Quando seek ≈ real, latência rotacional domina o tempo total.

## Cache no Controlador

A maioria dos controladores inclui cache interno:
- Usa **write-through**; não atualiza em write miss
- Inclui algoritmos de **prefetch** para antecipar demanda
- Complica medição de desempenho de disco

## Relações (SPO)

- Disco Magnético → armazena dados de forma → não volátil
- Setor → é unidade mínima de → leitura/escrita
- T_acesso → decompõe-se em → seek + rotacional + transferência + controlador
- ZBR → aumenta → capacidade de disco
- [[RAID]] → combina múltiplos → Discos Magnéticos para redundância/desempenho
- [[Teoria de Filas para IO]] → modela → fila de requisições ao Disco Magnético

## Ver também

- [[RAID]]
- [[Hierarquia de Memória]]
- [[Confiabilidade e Disponibilidade]]
- [[Teoria de Filas para IO]]
- [[DMA]]
