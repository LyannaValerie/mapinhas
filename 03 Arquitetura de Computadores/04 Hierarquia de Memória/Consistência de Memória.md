---
title: Consistência de Memória
aliases:
  - memory consistency
  - sequential consistency
  - TSO
  - total store order
  - modelos de consistência
  - memory model
  - release consistency
  - weak consistency
tags:
  - computação/arquitetura
date: 2026-04-22
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Consistência de Memória

## Definição

Contrato entre o modelo de programação e o hardware que define **quando** uma escrita de um processador se torna visível para os demais. Distinto de [[Coerência de Cache]] (que garante que cópias de um mesmo bloco sejam consistentes): consistência de memória define a ordem *entre escritas em blocos diferentes*.

## Relações (SPO)
- Consistência sequencial → garante → ordem global igual à ordem de programa de cada processador
- TSO → permite → writes ficarem no write buffer antes de propagar
- Modelo relaxado → exige → FENCE explícita para ordenar operações
- Consistência de liberação → usa → acquire/release como pontos de ordenação
- [[Sincronização em Multiprocessadores]] → depende de → modelo de consistência para semântica correta
- x86 → implementa → TSO (Total Store Order)
- ARM → implementa → modelo muito relaxado (weak ordering)

---

## O problema

```
// P1:          // P2:
A = 1;          B = 1;
r1 = B;         r2 = A;
```

Pode `r1 == 0` e `r2 == 0` simultaneamente? Em hardware com write buffers ou OoO execution, **sim** — a menos que o modelo de consistência proíba.

---

## Consistência Sequencial (SC)

**Definição (Lamport, 1979):** o resultado de qualquer execução é o mesmo que seria se as operações de todos os processadores fossem executadas em alguma **ordem sequencial total**, e as operações de cada processador individual aparecem nessa ordem na mesma sequência que o programa.

- Intuitivamente: cada processador vê suas próprias escritas e as dos outros na ordem em que ocorreram
- **Custo:** write buffer deve ser drenado antes de cada leitura; OoO limitado
- A maioria dos hardwares modernos **não** implementa SC por padrão

---

## Modelos relaxados

### TSO — Total Store Order (Processor Consistency)

- Escritas podem ficar em write buffer antes de propagar para a memória
- Uma leitura pode retornar valor do write buffer local (bypass)
- Mas escritas entre processadores distintos são vistas na mesma ordem por todos
- **x86** usa TSO (com pequenas exceções)
- Fix: `MFENCE` drena write buffer; `LOCK` prefix torna operação SC

### PSO — Partial Store Order

- Escritas de um mesmo processador também podem ser reordenadas entre si
- Necessário `FENCE` explícita para impor ordem entre escritas do mesmo processador

### Consistência Fraca (Weak Consistency)

- Nenhuma ordem garantida entre operações de memória arbitrárias
- Apenas **operações de sincronização** são totalmente ordenadas
- Entre duas sincronizações, o hardware pode reordenar livremente

### Consistência de Liberação (Release Consistency)

- Refinamento da consistência fraca: operações de sincronização diferenciadas em **acquire** e **release**
- `acquire`: garante que todas as leituras/escritas anteriores de outros processadores já propagaram
- `release`: garante que todas as escritas locais já propagaram antes de liberar o lock
- Implementação: `acquire` → LFENCE; `release` → SFENCE + sinalização
- Base conceitual de `std::atomic` em C++ e `volatile` em Java

---

## Comparação dos modelos

| Modelo | Reordenação W→R | Reordenação W→W | Reordenação R→W/R | FENCE necessária? |
|---|---|---|---|---|
| SC | Não | Não | Não | Não |
| TSO | Sim (local) | Não | Não | Sim (W→R entre procs) |
| PSO | Sim | Sim | Não | Sim |
| Weak/RC | Sim | Sim | Sim | Sim (entre sync ops) |

---

## Implicações práticas

> [!warning] Data races e UB
> Programas com data races (acesso concorrente sem sincronização a um mesmo local) têm comportamento indefinido em todos os modelos exceto SC. Sincronização correta via locks, atomics ou barreiras é obrigatória.

- **Portabilidade:** código correto em x86 (TSO) pode falhar em ARM (weak) sem FENCEs explícitas
- **Compiladores:** podem reordenar instruções independentemente do hardware — `volatile` e `atomic` inibem essa reordenação
- **Lock-free programming:** depende da semântica exata do modelo da arquitetura alvo

---

## Ver também
- [[Multiprocessadores]] — tabela resumo dos modelos de consistência em contexto de UMA/NUMA
- [[Sincronização em Multiprocessadores]] — primitivas que dependem do modelo (FENCE, LL/SC)
- [[Coerência de Cache]] — garante unicidade de cópia; consistência define *ordem* das escritas
- [[Thread]] — data races em programas Pthreads
