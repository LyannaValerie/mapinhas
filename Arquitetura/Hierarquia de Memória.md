---
title: Hierarquia de Memória
aliases:
  - memory hierarchy
  - pirâmide de memória
  - hierarquia de armazenamento
tags:
  - computação/arquitetura
date: 2026-04-06
---

# Hierarquia de Memória

## Princípio fundamental
> **Em cada nível, o armazenamento serve como cache para o nível imediatamente abaixo.**

Inserir um dispositivo menor e mais rápido entre o processador e um dispositivo maior e mais lento é uma ideia geral — todos os sistemas computacionais organizam seus dispositivos de armazenamento dessa forma.

## A hierarquia completa

```
         Menor, mais rápido, mais caro (por byte)
              ↑
L0   Registradores       — palavras do [[Processador]]
L1   Cache L1 (SRAM)     — linhas de cache vindas da L2
L2   Cache L2 (SRAM)     — linhas de cache vindas da L3
L3   Cache L3 (SRAM)     — linhas de cache vindas da RAM
L4   Memória principal (DRAM) — blocos vindos do disco
L5   Disco local (HDD/SSD) — arquivos vindos de servidores remotos
L6   Armazenamento remoto — sistemas de arquivos distribuídos, servidores Web
              ↓
         Maior, mais lento, mais barato (por byte)
```

## Níveis em detalhe

| Nível | Nome | Tecnologia | Capacidade típica | Gerenciado por |
|---|---|---|---|---|
| L0 | [[Registrador\|Registradores]] | — | ~centenas de bytes | Compilador |
| L1 | [[Cache]] L1 | SRAM | dezenas de KB | Hardware (automático) |
| L2 | [[Cache]] L2 | SRAM | centenas de KB a MB | Hardware (automático) |
| L3 | [[Cache]] L3 | SRAM | dezenas de MB | Hardware (automático) |
| L4 | [[Memória]] principal | DRAM | GB | SO + hardware |
| L5 | Disco local | Flash/magnético | TB | SO |
| L6 | Armazenamento remoto | Rede | PB+ | SO + rede |

## Relação de cache entre níveis

- O **arquivo de registradores** é cache para a L1
- **L1** é cache para L2; **L2** é cache para L3; **L3** é cache para a memória principal
- A **memória principal** é cache para o disco local
- O **disco local** é cache para armazenamento remoto (em sistemas com sistemas de arquivos distribuídos)

## Implicação para programadores

Programadores que compreendem a hierarquia de memória podem estruturar seu código para **explorar a localidade** — mantendo dados frequentemente acessados nos níveis mais altos (mais rápidos). Ganhos de desempenho de uma ordem de grandeza são possíveis.

Ver [[Cache#Princípio de funcionamento — Localidade]] para o princípio de localidade temporal e espacial.

## Ver também
- [[Cache]] — detalhes de L1/L2/L3, SRAM vs DRAM, gap processador-memória
- [[Memória]] — nível L4 (DRAM)
- [[Registrador]] — nível L0
- [[Memória de Massa]] — nível L5 (disco)
- [[Processador]] — consome dados dos níveis superiores
