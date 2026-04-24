---
title: TLB
aliases:
  - Translation Look-aside Buffer
  - cache de tradução
tags:
  - computação/arquitetura
  - computação/memória
date: 2026-04-22
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# TLB — Translation Look-aside Buffer

## Definição
Cache de hardware dentro da MMU que armazena traduções recentes de endereço virtual → físico. Evita percorrer a tabela de páginas na RAM a cada acesso à memória.

## Relações (SPO)
- TLB → é um → cache especializado de traduções de endereço
- TLB → parte de → MMU (Memory Management Unit)
- TLB hit → entrega → endereço físico sem acesso à RAM
- TLB miss → dispara → page table walk + atualização do TLB
- [[Memória Virtual]] → depende de → TLB para desempenho aceitável
- TLB → compõe → [[Hierarquia de Memória]] (nível entre registradores e L1)

## Estrutura

```
VA:  [ TLBT (tag) | TLBI (índice) | VPO (page offset) ]
                        ↓
                   lookup no TLB
                   ↓           ↓
                 hit           miss
                  ↓              ↓
                PPN         page table walk
                  ↓              ↓
              PA = PPN | VPO   atualiza TLB → PPN
```

| Campo | Função |
|---|---|
| TLBT | identifica qual VPN está na entrada |
| TLBI | seleciona conjunto (se set-associativo) |
| VPO | passa direto como page offset no endereço físico |

## Propriedades práticas
- Totalmente associativo ou set-associativo dependendo do nível (L1-TLB, L2-TLB)
- TLB flush necessário em troca de processo (ou uso de ASID para evitar)
- x86: write em `CR3` → flush implícito; `INVLPG` invalida entrada específica

## Ver também
- [[Memória Virtual]]
- [[Paginação x86]]
- [[Cache]]
- [[Hierarquia de Memória]]
