---
title: Coerência de Cache
aliases:
  - cache coherence
  - cache coherency
  - protocolo MESI
  - MESI
  - snooping cache
  - write-through protocol
  - write-back protocol
tags:
  - computação/arquitetura
date: 2026-04-13
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Coerência de Cache

## Definição

Em sistemas com múltiplas CPUs, cada CPU tem sua própria cache. Se duas CPUs armazenam a mesma linha de cache e uma delas escreve nela, a outra passa a ter **dados velhos** — violando o contrato de memória. Esse problema se chama **incoerência de cache**.

## Relações (SPO)
- Incoerência de cache → ocorre quando → múltiplas caches contêm cópias da mesma linha e uma é modificada
- Protocolo de coerência de cache → garante → que versões diferentes da mesma linha nunca existam simultaneamente em duas caches
- Cache de escuta → monitora → barramento para detectar acessos de outras CPUs
- MESI → é → protocolo de coerência write-back com 4 estados
- Write-through → garante consistência mantendo → memória principal sempre atualizada

---

## Caches de Escuta (Snooping Caches)

Cada controlador de cache **monitora o barramento** e reage a requisições de outras CPUs. Denominadas *snooping caches* ou *caches de espia*. O controlador age somente sobre o que observa no barramento — nada mais.

---

## Protocolo Write-Through (Escrita Direta)

| Ação local | Operação | Ação da cache remota (escuta) |
|---|---|---|
| Ausência para leitura | Busca da memória (sempre atualizada) | Nenhuma |
| Presença para leitura | Usa cache local | Nenhuma |
| Ausência para escrita | Escreve na memória | Invalida entrada se presente |
| Presença para escrita | Atualiza cache **e** memória | Invalida entrada se presente |

**Princípio:** toda escrita vai direto para a memória principal → memória sempre atualizada.

**Problema:** toda escrita gera tráfego no barramento → gargalo com muitas CPUs.

---

## Protocolo MESI (Write-Back)

Protocolo popular de escrita retroativa usado pelo **Core i7** e muitos outros processadores. Cada linha de cache pode estar em um de quatro estados:

| Estado | Significado |
|---|---|
| **M** — Modified | Válida; memória desatualizada; só existe aqui |
| **E** — Exclusive | Só nesta cache; memória atualizada |
| **S** — Shared | Em uma ou mais caches; memória atualizada |
| **I** — Invalid | Entrada sem dados válidos |

### Transições de estado

```
CPU1 lê linha A → [E] (exclusivo — única cópia)
CPU2 lê linha A → CPU1 passa para [S], CPU2 entra em [S]
CPU2 escreve A  → sinal de invalidação; CPU2 entra em [M], CPU1 inválida
CPU3 lê A       → CPU2 (M) escreve de volta na memória; ambos vão para [S]
CPU2 escreve A  → CPU3 inválida; CPU2 vai para [M]
CPU1 escreve A  → CPU2 (M) escreve de volta; CPU1 entra em [M]
```

### Vantagens do MESI sobre write-through
- Escritas repetidas na mesma linha não geram tráfego no barramento
- Linha em estado E pode ser escrita sem sinal de invalidação (nenhuma outra cópia existe)

---

## Estratégias de Atualização vs. Invalidação

Quando outra CPU escreve em uma linha presente na cache local:

| Estratégia | Comportamento | Trade-off |
|---|---|---|
| **Invalidação** | Marca a entrada como inválida | Mensagem pequena; próximo acesso causa ausência de cache |
| **Atualização** | Aceita o novo valor | Mensagem maior (carrega dado); evita ausência futura |

---

## Política de Alocação de Escrita

Quando ocorre **ausência de cache para escrita** (write miss):

- **Com alocação (write-allocate):** carrega a linha na cache antes de escrever → útil se a linha será escrita novamente em breve
- **Sem alocação:** escrita vai direto para a memória; linha não entra na cache

---

## Desempenho de Snooping em Multiprocessadores

A **taxa de miss de coerência (CMR)** mede o overhead de coerência sobre o tráfego total de cache. Para 8 processadores a 3 GHz com CPI base e custo de 15 ciclos por CMR:

$$\text{CMR}_\text{max} \approx 0{,}73\%$$

CMR > ~1% satura o barramento. Técnicas para escalar largura de banda de snooping:
1. **Duplicar tags** — segunda cópia responde a snoops sem bloquear dados
2. **L3 distribuída (NUCA)** — cada processador gerencia snoops do seu banco (IBM Power8)
3. **Diretório no L3** — L3 inclusiva filtra snoops; invalida só quem o diretório indica (Intel Xeon E7)

Quando o barramento satura estruturalmente, a solução é [[Protocolo de Diretório]].

---

## Ver também
- [[Hierarquia de Memória]] — cache L1/L2/L3 e latências
- [[Multiprocessadores]] — onde a coerência é exigida (UMA, NUMA)
- [[Protocolo de Diretório]] — coerência sem broadcast; escala para NUMA
- [[Falso Compartilhamento]] — miss de coerência sem compartilhamento real de palavra
- [[3 Cs de Cache]] — miss de coerência = 4° C em multiprocessadores
- [[Concorrência e Paralelismo]] — multiprocessadores e ILP
- [[Cache]] — conceito básico de cache
