---
title: Hierarquia de Memória
aliases:
  - memory hierarchy
  - pirâmide de memória
  - hierarquia de armazenamento
tags:
  - computação/arquitetura
date: 2026-04-06
source: "Livro Computer Architecture, Elsevier, 2019, cap. 2"
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Hierarquia de Memória

## Princípio fundamental
> **Em cada nível, o armazenamento serve como cache para o nível imediatamente abaixo.**

Inserir um dispositivo menor e mais rápido entre o processador e um dispositivo maior e mais lento é uma ideia geral — todos os sistemas computacionais organizam seus dispositivos de armazenamento dessa forma.

## Problema de projeto

Projetar [[Hierarquia de Memória]] é equilibrar quatro variáveis em múltiplos níveis:

- **capacidade**
- **latência**
- **largura de banda**
- **custo por bit**

Trade-off central:

- níveis superiores oferecem **menor latência** e **menor capacidade**;
- níveis inferiores oferecem **maior capacidade** e **maior latência**.

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

| Nível | Nome                  | Tecnologia      | Capacidade típica   | Gerenciado por        |            |
| ----- | --------------------- | --------------- | ------------------- | --------------------- | ---------- |
| L0    | [[Registrador]]       | Registradores]] | —                   | ~centenas de bytes    | Compilador |
| L1    | [[Cache]] L1          | SRAM            | dezenas de KB       | Hardware (automático) |            |
| L2    | [[Cache]] L2          | SRAM            | centenas de KB a MB | Hardware (automático) |            |
| L3    | [[Cache]] L3          | SRAM            | dezenas de MB       | Hardware (automático) |            |
| L4    | [[Memória]] principal | DRAM            | GB                  | SO + hardware         |            |
| L5    | Disco local           | Flash/magnético | TB                  | SO                    |            |
| L6    | Armazenamento remoto  | Rede            | PB+                 | SO + rede             |            |

## Relação de cache entre níveis

- O **arquivo de registradores** é cache para a L1
- **L1** é cache para L2; **L2** é cache para L3; **L3** é cache para a memória principal
- A **memória principal** é cache para o disco local
- O **disco local** é cache para armazenamento remoto (em sistemas com sistemas de arquivos distribuídos)

## Tipos de Miss

| Tipo | Condição |
|---|---|
| **Cold miss** (frio/compulsório) | Primeiro acesso a qualquer bloco — inevitável |
| **Conflict miss** (conflito) | Cache tem espaço, mas blocos concorrem pelo mesmo conjunto |
| **Capacity miss** (capacidade) | Working set excede tamanho da cache |

## Onipresença do Cache

| Tipo de cache | O que armazena | Onde |
|---|---|---|
| Registradores CPU | Palavras de 4–8 B | Chip |
| TLB | Traduções de endereço | Chip (MMU) |
| L1/L2/L3 | Blocos de 64 B | Chip (SRAM) |
| Memória virtual | Páginas de 4 KB | RAM (SO + hardware) |
| Buffer cache | Partes de arquivos | RAM (SO) |
| Cache de disco | Setores | Controlador |
| Cache de rede/browser | Páginas Web | Disco local |

## Memory Mountain

Função 2D que caracteriza o desempenho do sistema de memória: **throughput de leitura** vs **tamanho do working set** (eixo temporal) e **stride** (eixo espacial).

- **Cristas** perpendiculares ao stride = regiões onde o working set cabe em L1, L2, L3 ou RAM
- **Declives** ao longo do stride = queda de throughput conforme stride aumenta (pior localidade espacial)
- Core i7 Haswell: pico L1 ~14 GB/s; mínimo RAM ~900 MB/s → **>1 ordem de grandeza** de diferença
- Stride-1 com prefetching mantém throughput elevado mesmo com working set > L2

## Implicação para programadores

Programadores que compreendem a hierarquia de memória podem estruturar seu código para **explorar a localidade** — mantendo dados frequentemente acessados nos níveis mais altos (mais rápidos). Ganhos de desempenho de uma ordem de grandeza são possíveis.

Ver [[Otimização de Código#Localidade e Cache]] para técnicas concretas.

## Ver também
- [[Cache]] — detalhes de L1/L2/L3, SRAM vs DRAM, gap processador-memória
- [[Memória]] — nível L4 (DRAM)
- [[Memória Virtual]] — estende hierarquia ao disco com tradução de endereços
- [[Registrador]] — nível L0
- [[Memória de Massa]] — nível L5 (disco)
- [[Processador]] — consome dados dos níveis superiores
