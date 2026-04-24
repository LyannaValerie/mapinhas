---
title: Protocolo de Diretório
aliases:
  - directory protocol
  - directory-based coherence
  - DSM coherence
  - NUMA coherence
tags:
  - computação/arquitetura
date: 2026-04-22
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Protocolo de Diretório

## Definição

Mecanismo de [[Coerência de Cache]] sem broadcast: um **diretório** rastreia quais caches têm cópia de cada bloco de memória e envia mensagens ponto a ponto apenas para os envolvidos. Alternativa ao [[Coerência de Cache#Caches de Escuta (Snooping Caches)|snooping]] quando o barramento compartilhado não é viável.

## Relações (SPO)
- Protocolo de diretório → elimina → broadcast de snooping
- Protocolo de diretório → escala para → sistemas com centenas–milhares de processadores
- Entrada de diretório → contém → estado do bloco + conjunto de sharers
- NUMA → usa → protocolo de diretório como mecanismo de coerência
- Intel Xeon E7 → implementa → diretório no nível L3 (8–32 cores)
- AMD Opteron → combina → snooping ponto-a-ponto com elementos de diretório

---

## Motivação

Snooping exige barramento único (ou broadcast) para que todas as caches ouçam toda operação. Com dezenas de processadores, o barramento satura. O protocolo de diretório resolve isso: cada bloco tem um **registro de estado + sharers** que indica exatamente quem precisa ser notificado.

---

## Estrutura da entrada de diretório

Para cada bloco de memória:

| Campo | Conteúdo |
|---|---|
| **Estado** | U (uncached), S (shared), M (modified/exclusive) |
| **Sharers** | Bitset de processadores com cópia válida |

Overhead de memória: ~2–4 bits de estado + n bits de sharers por bloco. Para 64 processadores e blocos de 64 bytes: ≈ 1% da memória total.

---

## Estados e transições

```
U (Uncached) ──read miss──▶ S (Shared)
U            ──write miss─▶ M (Modified)
S            ──write miss─▶ M  (+ invalidar todos os sharers)
M            ──read miss──▶ S  (+ writeback do dono atual)
M            ──write miss─▶ M  (+ writeback + nova propriedade)
```

### Mensagens no protocolo

| Evento | Ação |
|---|---|
| **Read miss** | Diretório envia bloco ao solicitante; adiciona ao conjunto de sharers; estado → S |
| **Write miss (estado U)** | Diretório envia bloco; estado → M; único sharer = solicitante |
| **Write miss (estado S)** | Diretório envia invalidação a todos os sharers; espera ACKs; envia bloco; estado → M |
| **Write miss (estado M)** | Diretório pede writeback ao dono; recebe bloco; envia a novo dono; estado → M |
| **Replacement (M→U)** | Dono envia writeback; estado → U; sharers = ∅ |

---

## Vantagens sobre snooping

| Aspecto | Snooping | Diretório |
|---|---|---|
| Comunicação | Broadcast | Ponto a ponto (unicast) |
| Escalabilidade | Limitada (barramento) | Alta (rede de interconexão) |
| Latência (caso local) | Menor | Maior (protocolo mais pesado) |
| Complexidade | Menor | Maior |

---

## Implementações

- **Intel Xeon E7** (8–32 cores): diretório no nível L3; L3 age como filtro de snoop e deve ser inclusivo
- **AMD Opteron multi-chip**: ponto a ponto via HyperTransport; combina elementos de snooping e diretório; NUMA com acesso local mais rápido
- **IBM Power8** (12 cores): L3 distribuída (NUCA); diretório por banco de L3

### DSM — Distributed Shared-Memory

Implementação em software ou hardware de memória compartilhada sobre hardware sem compartilhamento físico. O protocolo de diretório é o mecanismo de coerência típico em DSM por hardware.

---

## Ver também
- [[Coerência de Cache]] — snooping, protocolo MESI, estratégias de invalidação/atualização
- [[Multiprocessadores]] — UMA, NUMA, CC-NUMA
- [[Hierarquia de Memória]] — estrutura L1/L2/L3 e inclusividade
- [[Falso Compartilhamento]] — degradação de desempenho mesmo com diretório
