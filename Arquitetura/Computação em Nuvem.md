---
title: Computação em Nuvem
aliases:
  - cloud computing
  - nuvem
  - computação em nuvem
tags:
  - computação/arquitetura
date: 2026-04-06
---

# Computação em Nuvem

## Definição
Modelo em que dados e processamento residem em **data centers** remotos (a "nuvem"), acessíveis por dispositivos simples (PC, tablet, smartphone) a partir de qualquer lugar. Denominado "mainframe 2.0" — retorno ao paradigma centralizado, mas em escala massiva distribuída.

## Relações (SPO)
- Computação em Nuvem → baseada em → [[Multicomputadores e Clusters|clusters]] e data centers
- Computação em Nuvem → representa → retorno à centralização (após era do PC descentralizado)
- Computação em Nuvem → substitui → armazenamento local de dados no dispositivo do usuário
- Computação em Nuvem → análoga historicamente a → modelo de mainframe + terminais (anos 1960)

## O pêndulo centralização ↔ descentralização

```
Anos 1960  →  Mainframes centralizados + terminais simples
Anos 1980  →  PCs descentralizados (cada um com seus dados)
Anos 2000+ →  Cloud computing: data centers + dispositivos simples  ← AGORA
Futuro?    →  Nova descentralização (gargalo de rede)?
```

> [!info] A lição do pêndulo
> O modelo de computação popular depende da tecnologia, da economia e das aplicações disponíveis do momento. Pode mudar quando esses fatores mudarem.

## Infraestrutura: Clusters e Data Centers

**Cluster:** conjunto de servidores COTS (Commodity Off The Shelf) conectados por redes de gigabits/s, com software para operar como uma única máquina. Permite processamento paralelo de problemas científicos ou web.

**Data center:** sala ou prédio que abriga o cluster.
- Escala: de dezenas a **milhares** de servidores
- Fator limitador: verba disponível
- Exige grandes quantidades de energia elétrica

> [!example] Google em The Dalles, Oregon
> Data center do tamanho de dois campos de futebol americano. Localizado próximo a uma represa hidrelétrica de 2 GW no Rio Colúmbia. A Google estima mais de **1 milhão de servidores** no total de seus data centers.

## Uso para servidores web
Milhares de requisições por segundo → distribuídas entre centenas/milhares de servidores em paralelo. Mais econômico do que um único supercomputador.

## Ver também
- [[Tipos de Computadores]]
- [[História da Computação]]
- [[Sistema Operacional]]
