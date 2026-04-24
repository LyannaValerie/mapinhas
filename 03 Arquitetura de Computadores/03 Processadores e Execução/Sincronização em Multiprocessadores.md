---
title: Sincronização em Multiprocessadores
aliases:
  - multiprocessor synchronization
  - lock multiprocessador
  - LL/SC
  - load-linked store-conditional
  - test-and-set
  - barreira de sincronização
tags:
  - computação/arquitetura
date: 2026-04-22
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Sincronização em Multiprocessadores

## Definição

Mecanismos para coordenar acesso concorrente a dados compartilhados em [[Multiprocessadores]]. O desafio central: operações de leitura-modificação-escrita precisam ser **atômicas** — indivisíveis mesmo com múltiplos processadores ativos simultaneamente.

## Relações (SPO)
- Test-and-Set → implementa → spin lock via instrução atômica
- LL/SC → é → alternativa RISC a Test-and-Set; composável sem deadlock
- Barreira → força → que todos os processadores cheguem ao ponto antes de prosseguir
- FENCE → garante → ordenação de operações de memória (anti-reordenação)
- [[Consistência de Memória]] → afeta → semântica das operações de sincronização

---

## O problema

Em uniprocessadores, basta desabilitar interrupções para garantir atomicidade. Em multiprocessadores, outros processadores continuam rodando — interrupções locais não bastam. Necessário: **suporte de hardware** via instruções atômicas.

---

## Test-and-Set (TAS)

Lê o valor de um endereço e escreve 1 atomicamente em uma única operação de barramento.

```
lock: TAS R1, mutex   # R1 ← mem[mutex]; mem[mutex] ← 1
      BNEZ R1, lock   # se R1 != 0, ainda trancado → retry
      # seção crítica
      STR mutex, #0   # libera
```

### Problema: bus saturation

Enquanto o lock está ocupado, cada processador emite `TAS` repetidamente → tráfego de barramento mesmo sem progresso → degrada desempenho de todos.

### Test-and-Test-and-Set (TTAS)

Gira lendo o valor cacheado (sem tráfego de barramento); só emite `TAS` quando o lock parece livre:

```
wait:  LDR R1, mutex     # leitura cacheada — sem tráfego
       BNEZ R1, wait     # gira em cópia local
       TAS R1, mutex     # tenta atomicamente
       BNEZ R1, wait     # falhou → volta a girar
```

- Reduz tráfego: `TAS` só emitido quando há chance de sucesso
- Ainda gera rajada de `TAS` quando lock é liberado (thundering herd)

---

## Load-Linked / Store-Conditional (LL/SC)

Par de instruções atômico sem precisar de instrução de leitura-escrita única:

| Instrução | Operação |
|---|---|
| `LL Rd, addr` | Carrega valor; marca bloco como *reserved* (exclusivo na cache) |
| `SC Rs, addr` | Armazena *somente se* reserva ainda válida; retorna 1 (sucesso) ou 0 (falha) |

A reserva é invalidada se outro processador escrever no bloco entre `LL` e `SC`.

```asm
retry:  LL  R1, mutex     # carrega; reserva
        BNEZ R1, retry    # lock ocupado?
        DADDUI R2, R0, #1
        SC  R2, mutex     # tenta adquirir
        BEQZ R2, retry    # falhou → retry
        # seção crítica
        STR mutex, #0     # libera
```

### Vantagens sobre TAS
- **Composável**: pode implementar qualquer primitiva atômica (swap, fetch-and-add, CAS)
- **Sem livelock garantido por hardware** (implementação correta não garante por software, mas é tratável)
- Padrão em arquiteturas RISC: MIPS, PowerPC, ARM, RISC-V

---

## Fetch-and-Increment / Compare-and-Swap

| Operação | Semântica |
|---|---|
| `FAI(addr)` | Retorna valor atual; incrementa atomicamente |
| `CAS(addr, old, new)` | Se `mem[addr] == old`, troca por `new`; retorna valor antigo |

`CAS` é suficiente para implementar qualquer estrutura lock-free. Disponível em x86 como `CMPXCHG`.

---

## Barreiras (Barriers)

Ponto de sincronização: nenhum processador prossegue além da barreira até que **todos** tenham chegado.

```
// Implementação ingênua com contador compartilhado
lock(barrier_lock)
  barrier_count++
  if barrier_count == num_procs:
    barrier_count = 0
    barrier_flag = !barrier_flag  # inverte sinal
unlock(barrier_lock)
spin until local_flag == barrier_flag  # espera todos
```

Uso: separar fases de computação em algoritmos paralelos (ex: multiplicação de matrizes por blocos).

---

## Queuing Locks (Locks com Fila)

Solução para o problema de thundering herd e contention em [[Multiprocessadores de Grande Escala]]:

- Cada processador em espera gira num **flag privado** (por processador), não na variável de lock global
- Ao liberar o lock, o detentor sinaliza apenas o **próximo** processador na fila
- Resultado: O(1) invalidações de cache por release (vs. O(N) com TAS/TTAS)

**Implementação básica:**
```
// Processador entra na fila com fetch-and-increment
meu_slot = FAI(ticket_counter)
spin while position[meu_slot % N] == LOCKED
// seção crítica
position[(meu_slot+1) % N] = UNLOCKED  // libera próximo
```

Variantes: MCS lock, CLH lock — mantêm fila de forma que cada processador gira em variável própria.

## Exponential Backoff

Reduz contention em spin locks quando há muitos competidores:

```asm
lockit:
        DADDUI R3, R0, #1    ; R3 = delay inicial
gotit:
        LL     R2, 0(R1)
        BNEZ   R2, lockit    ; lock ocupado → spinning
        DADDUI R2, R2, #1
        SC     R2, 0(R1)
        BNEZ   R2, gotit     ; SC falhou → já tem lock
        DSLL   R3, R3, #1    ; dobra o delay
        PAUSE  R3            ; espera R3 ciclos
        J      lockit        ; tenta novamente
```

- Ao falhar, processador espera `R3` ciclos antes de tentar novamente
- A cada falha, delay dobra (exponential backoff)
- Reduz tráfego no barramento/rede de interconexão
- Delay inicial ≈ tempo de seção crítica

## Barreiras em Árvore (Tree-Based Barriers)

Para barreiras com muitos processadores, barreira centralizada cria hot spot:

```
// Árvore de combinação bottom-up:
// Cada folha aguarda; ao chegar, sinaliza pai
// Raiz: todos chegaram → broadcast de release (top-down)
```

- Reduz contention: cada nó da árvore é acessado por apenas K processadores (fator de fanout)
- `O(log N)` operações em vez de `O(N)` no contador central
- Técnica **sense-reversing**: alterna flag a cada uso para evitar reset explícito
- Suporte em hardware: T3D e CM-5 incluíam hardware de barreira; máquinas recentes usam bibliotecas de software

## FENCE / Memory Barrier

Instrução que impede o processador (e compilador) de reordenar operações de memória ao redor dela.

- Necessária em modelos relaxados ([[Consistência de Memória#Modelos relaxados|TSO, RC]])
- Em x86: `MFENCE` (full), `SFENCE` (store), `LFENCE` (load)
- Frequentemente inserida implicitamente em operações atômicas

---

## Ver também
- [[Multiprocessadores]] — contexto: por que sincronização é necessária
- [[Multiprocessadores de Grande Escala]] — queuing locks e barreiras em árvore são essenciais nessa escala
- [[Consistência de Memória]] — modelo de memória afeta semântica das operações
- [[Coerência de Cache]] — a reserva do LL/SC usa estado exclusivo de cache
- [[Semáforo]] — P/V; implementação em multiprocessador usa TAS ou LL/SC
- [[Thread]] — threads compartilham espaço de endereços; sincronização via pthreads
