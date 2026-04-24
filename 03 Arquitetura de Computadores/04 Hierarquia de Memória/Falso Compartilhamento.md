---
title: Falso Compartilhamento
aliases:
  - false sharing
  - false sharing miss
  - compartilhamento falso
tags:
  - computação/arquitetura
date: 2026-04-22
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Falso Compartilhamento

## Definição

Tipo de **miss de coerência** que ocorre quando dois processadores escrevem em palavras **diferentes** que residem no **mesmo bloco de cache**. O bloco é "compartilhado" no sentido protocolar (invalidações são emitidas), mas nenhuma palavra individual é realmente compartilhada — o miss não ocorreria se o bloco tivesse tamanho de 1 palavra.

## Relações (SPO)
- Falso compartilhamento → causa → invalidações desnecessárias entre caches
- Falso compartilhamento → aumenta → taxa de miss de coerência (CMR)
- Falso compartilhamento → distingue-se de → verdadeiro compartilhamento (mesma palavra acessada por múltiplos processadores)
- Padding de dados → mitiga → falso compartilhamento ao alinhar estruturas ao tamanho do bloco

---

## Verdadeiro × Falso Compartilhamento

| Tipo | Definição | Ocorreria com bloco de 1 palavra? |
|---|---|---|
| **Verdadeiro compartilhamento** | Dois processadores acessam a **mesma palavra** (ao menos um escreve) | Sim |
| **Falso compartilhamento** | Dois processadores acessam **palavras diferentes** no mesmo bloco (ambos escrevem) | Não |

---

## Exemplo

Bloco de cache contém palavras `z1` e `z2`. Bloco está em estado **Shared** (S) nas caches de P1 e P2.

| Evento | Miss? | Tipo |
|---|---|---|
| P1 lê `z1` | Não — hit | — |
| P2 escreve `z2` | Miss em P1 (invalidação do bloco inteiro) | **Falso compartilhamento** — P1 não usa `z2` |
| P1 escreve `z1` | Miss em P2 (bloco invalidado por P2) | **Falso compartilhamento** |
| P1 lê `z2` depois de P2 escrever | Miss | **Verdadeiro compartilhamento** |

O bloco ping-pong entre as caches de P1 e P2, gerando tráfego de barramento sem necessidade real.

---

## Impacto no desempenho

- **Taxa de miss de coerência (CMR)** aumenta → degrada largura de banda de snooping
- Em workloads com alto compartilhamento, CMR pode exceder 1% → satura barramento mesmo com poucos processadores
- Efeito de blocos maiores: blocos maiores aumentam probabilidade de falso compartilhamento (mais palavras por bloco = mais chance de conflito de layout)

---

## Mitigação

1. **Padding**: inserir bytes de preenchimento para que cada estrutura ocupe um bloco inteiro de cache

```c
// Sem padding — z1 e z2 podem estar no mesmo bloco
struct { int z1; int z2; } shared;

// Com padding — cada variável em seu próprio bloco (64 bytes tipicamente)
struct { int z1; char pad[60]; } p1_data;
struct { int z2; char pad[60]; } p2_data;
```

2. **Alinhamento**: garantir que estruturas per-thread comecem em fronteira de bloco (`alignas(64)`)

3. **Reestruturação de dados**: separar campos acessados por threads diferentes em structs distintas

---

## Ver também
- [[Coerência de Cache]] — protocolo MESI, invalidação, estados de bloco
- [[Multiprocessadores]] — contexto de SMP onde falso compartilhamento ocorre
- [[3 Cs de Cache]] — miss de coerência é o 4° C (adicionado em multiprocessadores)
- [[Protocolo de Diretório]] — falso compartilhamento ocorre também em NUMA com diretório
