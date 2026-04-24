---
title: Cray X1
tags:
  - hardware
  - arquitetura
  - DLP
  - vetor
  - supercomputador
aliases:
  - Cray X1
  - MSP
  - SSP
  - Multi-Streaming Processor
  - Single-Streaming Processor
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Cray X1

Supercomputador vetorial introduzido em 2002. Com o NEC SX/8, representa o estado da arte em vetorial moderno. Primeira ISA Cray redesenhada desde o [[Arquitetura Vetorial|Cray-1 (1976)]].

Fonte: P&H "Computer Architecture: A Quantitative Approach", Apêndice G.7.

## Hierarquia de processamento

```
Sistema (até 1024 nós)
└── Nó: 4 MSPs + 16 memory controllers + DRAM (>200 GB/s)
    └── MSP (Multi-Streaming Processor): 4 SSPs + 2 MB Ecache compartilhado
        └── SSP (Single-Streaming Processor): 1 chip
            ├── Unidade escalar (400 MHz)
            │   ├── Dual-issue out-of-order superscalar
            │   ├── I-cache: 16 KB, 2-way set-associative, linhas 32B
            │   └── D-cache: 16 KB, write-through, 2-way, linhas 32B
            └── Unidade vetorial (800 MHz — 2× clock escalar)
                ├── 2 lanes
                ├── 3 unidades aritméticas vetoriais
                └── 1 unidade load-store vetorial
```

**Peak per MSP**: 12,8 GFLOPS.

## ISA do Cray X1

Redesenhada do zero — lições de 30 anos de pesquisa de compiladores e microarquitetura:

| Recurso | Quantidade |
|---|---|
| Registradores escalares de endereço | 64 × 64 bits |
| Registradores escalares de dados | 64 × 64 bits |
| Registradores vetoriais | 32 × 64 elementos × 64 bits/elemento |
| Registradores de máscara vetorial | 8 × 1 bit/elemento |

- Instruções de **tamanho fixo** (vs. comprimento variável anterior): simplifica superscalar fetch/decode
- Mais registradores → compilador mapeia mais variáveis → menos tráfego de memória + melhor escalonamento estático

## Ecache

- 2 MB compartilhado entre os 4 SSPs do MSP (0,5 MB por SSP via chip dedicado)
- 2-way set-associative, linhas 32B, **write-back**
- Pode armazenar dados vetoriais (explora temporal locality)
- Variante **non-allocating**: load/store sem alocar no Ecache para dados sem localidade
- Largura de banda: >50 GB/s por MSP (1 word/lane/ciclo @ 800 MHz)
- **Restrição de coerência**: Ecache faz cache apenas de DRAM **local ao nó**

## Nó X1

- 4 MSPs + 16 chips de memory controller + DRAM
- Cada chip controller: 8 canais Rambus DRAM × 1,6 GB/s = 12,8 GB/s por chip
- 128 canais total → **>200 GB/s** de largura de banda de memória principal
- Coerência entre os 4 Ecaches do nó: esquema de **diretório** (ver [[Protocolo de Diretório]])

## Sistema completo

- Até 1024 nós = 4096 MSPs = 16.384 SSPs
- Rede global de alta largura de banda via memory controller chips
- Acesso global por load/store (shared memory) — mais rápido que message-passing de clusters
- Nós remotos **não** fazem cache de dados locais
- Store remoto invalida Ecache local antes de atualizar memória

## Multi-Streaming Processor (MSP) — modos de uso

Conceito introduzido no SV1, aprimorado no X1: 4 SSPs de 2 lanes emulam 1 processador de 8 lanes.

**Modo 1 — emulação de 8 lanes** (inner loop longo):
- Compilador divide iterações entre SSPs: iters 0–249 → SSP0, 250–499 → SSP1, etc.
- Barreira de sincronização entre SSPs ao fim de cada loop nest
- Eficiente quando inner loop tem muitos elementos

**Modo 2 — paralelismo de outer loop** (inner loop curto):
- SSP0 processa linhas 0, 4, 8, ...; SSP1 processa linhas 1, 5, 9, ...
- Cada SSP vê vetor mais longo; paraleliza o overhead escalar
- Útil quando inner loop menor que MVL do MSP (256 elementos como 8-lane)

**Modo 3 — SSPs independentes** (TLP):
- Pouco paralelismo vetorial, mas alto thread-level parallelism
- Cada SSP executa thread independente

## Tolerância a latência de memória

| | Cray X1 MSP | Superscalar típico (2011) |
|---|---|---|
| Cache misses in-flight | **2048** (512/SSP) | 8–16 |
| Tamanho da cache line | **32 bytes** | 128+ bytes |

Superscalares usam linhas maiores para amortizar latência com mais dados por miss.
X1 usa linhas curtas + muitos misses simultâneos:
- Minimiza desperdício de banda em acessos non-unit stride com grandes datasets
- Vetorial gera e rastreia muitos requests simultaneamente com hardware simples

## Cray X1E (2004/2005)

- Nova tecnologia de fabricação: **2 SSPs por chip**
- **Primeiro chip vetorial multicore** (primeiro multiprocessador vetorial em um único chip)
- Cada nó físico: 8 MSPs → organizado como **2 nós lógicos de 4 MSPs** (mantém modelo de programação do X1)
- Clock: 400 → 565 MHz escalar; 800 → 1130 MHz vetorial
- Peak melhorado: 18 GFLOPS por MSP

## Relações (SPO)

- SSP → compõe → MSP (4 SSPs por MSP)
- MSP → compartilha → 2 MB Ecache entre os 4 SSPs
- Ecache → cache apenas → DRAM local ao nó
- Protocolo de diretório → mantém coerência → entre Ecaches do mesmo nó
- Store remoto → invalida → Ecache local antes de escrever
- X1E → introduz → primeiro chip vetorial multicore
- MSP modo 1 → emula → processador de 8 lanes via 4 × 2 lanes
- 2048 requests in-flight → compensa → alta latência de DRAM

## Ver também

- [[Arquitetura Vetorial]] — conceitos base: convoy, chime, chaining, lanes, strip-mining
- [[Desempenho de Processadores Vetoriais]] — R∞, N1/2, tailgating, dead time
- [[Sistema de Memória Vetorial]] — bancos, conflitos, DRAM vs. SRAM
- [[Protocolo de Diretório]] — coerência distribuída entre nós
- [[Hierarquia de Memória]] — cache, largura de banda, DRAM
- [[WSC — Warehouse Scale Computer]] — escala de datacenter vs. supercomputador vetorial
