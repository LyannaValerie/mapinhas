---
title: Semáforo
aliases:
  - semaphore
  - up/down
  - P/V
  - P e V
  - sem_wait
  - sem_post
  - mutex
  - exclusão mútua
  - condição de disputa
  - race condition
  - produtor-consumidor
  - sincronização de processos
  - variável de condição
  - seção crítica
  - grafo de progresso
  - região proibida
  - leitores-escritores
  - readers-writers
tags:
  - computação/arquitetura
date: 2026-04-13
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Sistemas de Software]]

# Semáforo

## Definição
Variável inteira não-negativa usada para sincronizar [[Processo|processos]] paralelos. Proposta por Dijkstra (1968). Duas operações **atômicas** operam sobre semáforos: **up** e **down**. A atomicidade garante que sinais de despertar nunca sejam perdidos — diferenciando semáforos de soluções ad hoc como flags e busy-wait.

## Relações (SPO)
- Semáforo → proposto por → Dijkstra (1968)
- Semáforo → resolve → condição de disputa entre processos/threads
- Semáforo → operações → up/V (incrementa) e down/P (decrementa ou bloqueia)
- Semáforo → propriedade fundamental → atomicidade (indivisibilidade)
- Semáforo → invariante → valor nunca negativo (s ≥ 0)
- Mutex → é → semáforo binário (valores 0 e 1)
- Mutex → cria → região proibida que envolve região insegura no grafo de progresso
- Semáforo → resolve → problema produtor-consumidor com buffer limitado (sbuf)
- Semáforo → resolve → problema leitores-escritores
- Grafo de progresso → modela → execução concorrente como trajetória em espaço n-dimensional

---

## Condição de disputa (Race Condition)

**Problema:** dois processos paralelos compartilham um recurso. O resultado correto depende da ordem de execução das instruções — que varia de execução para execução.

### Exemplo: produtor-consumidor com buffer circular

Dois processos compartilham buffer de 100 posições com ponteiros `in` (produtor) e `out` (consumidor):
- **Produtor:** gera números primos, coloca no buffer; vai dormir se buffer cheio
- **Consumidor:** retira números, imprime; vai dormir se buffer vazio

**O bug com `suspend/resume` simples:**

```
t=1: in=out=22 (buffer vazio)
t=2: consumidor lê in e out → buffer vazio → ainda não chamou suspend()
t=3: produtor deposita primo → in=23, out=22 → descobre in=out+1 → consumidor
     "dormindo" → chama resume() → sinal PERDIDO (consumidor ainda acordado)
t=4: consumidor chama suspend() → dorme
t=5: produtor continua, enche buffer (in=22, out=22) → chama suspend()
     → DEADLOCK: ambos dormindo para sempre
```

**Raiz do problema:** `if (in == out) suspend()` não é atômica — outro processo pode interferir entre a verificação e o suspend.

---

## Notação P/V (CS:APP)

CS:APP usa notação de Dijkstra: **P** (*proberen* — testar) = down; **V** (*verhogen* — incrementar) = up.

| Notação | Equivalência | API POSIX |
|---|---|---|
| P(s) / down(s) | Decrementa ou bloqueia | `sem_wait(&s)` |
| V(s) / up(s) | Incrementa, acorda um bloqueado | `sem_post(&s)` |

```c
#include <semaphore.h>
sem_t mutex;
sem_init(&mutex, 0, 1);   // inicializa com valor 1 (mutex)
sem_wait(&mutex);          // P — lock
// seção crítica
sem_post(&mutex);          // V — unlock
```

**Invariante do semáforo:** `s` nunca é negativo. Garante que um `V` que ocorra antes de um `P` correspondente não seja perdido — fica armazenado no valor do semáforo.

---

## Operações up e down

| Operação | Semáforo = 0 | Semáforo > 0 |
|---|---|---|
| **down** | Processo **bloqueia** (entra em fila) até outro executar up | `semáforo = semáforo − 1`; processo continua |
| **up** | `semáforo = semáforo + 1`; libera um processo da fila (se houver) | `semáforo = semáforo + 1` |

**Propriedade essencial:** uma vez iniciada a operação, nenhum outro processo acessa o mesmo semáforo até que a operação conclua ou o processo seja bloqueado. Sinais de despertar nunca são perdidos — são contados no semáforo.

> [!tip] Analogia das bolas de voleibol
> Cesto com bolas = semáforo. Colocar bola = up; retirar bola = down. Se o cesto estiver vazio, o jogador espera junto ao cesto até alguém devolver uma bola. A bola não some — fica guardada até ser retirada.

---

## Solução com semáforos: produtor-consumidor

Dois semáforos substituem a lógica de suspend/resume:
- `available = 100` (vagas livres no buffer, começa cheio)
- `filled = 0` (itens prontos para consumo, começa vazio)

```java
// Produtor
prime = next_prime(prime);     // P1: calcula próximo primo
down(available);               // P2: bloqueia se buffer cheio
buffer[in] = prime;            // P3: deposita
in = next(in);                 // P4: avança ponteiro
up(filled);                    // P5: sinaliza item disponível

// Consumidor
down(filled);                  // C1: bloqueia se buffer vazio
emirp = buffer[out];           // C2: retira
out = next(out);               // C3: avança ponteiro
up(available);                 // C4: sinaliza vaga liberada
System.out.println(emirp);     // C5: processa
```

**Por que funciona?** O `down` é atômico — registra a intenção de esperar antes de verificar a condição. Se o produtor fizer `up(filled)` antes de o consumidor executar `down(filled)`, o semáforo ficará em 1 e o consumidor não precisará bloquear. O sinal não é perdido.

---

## Implementação

**Uniprocessador:** desabilitar interrupções durante a operação de semáforo — garante que nenhuma troca de contexto ocorra no meio da operação.

**Multiprocessador:** desabilitar interrupções não basta (outras CPUs continuam rodando). Requer instruções atômicas de hardware: `compare-and-swap`, `test-and-set`, `fetch-and-add`.

---

## Grafo de progresso e regiões de segurança

**Grafo de progresso** modela execução de n threads concorrentes como trajetória em espaço n-dimensional. Cada eixo k = progresso da thread k.

Para duas threads que compartilham contador com seções críticas (L, U, S = load/update/store):

```
Thread 2
  T2 ─────────────────────────────
  S2 │         ┌──────────┐       │
  U2 │         │  UNSAFE  │       │
  L2 │         └──────────┘       │
  H2 │                             │
     H1  L1  U1  S1  T1
              Thread 1
```

| Conceito | Definição |
|---|---|
| **Seção crítica** | Instruções (L, U, S) que manipulam variável compartilhada — não devem ser intercaladas entre threads |
| **Região insegura** | Interseção das seções críticas no grafo; trajetória que a toca produz resultado incorreto |
| **Trajetória segura** | Contorna a região insegura; produz resultado correto |
| **Região proibida** | Estados onde `s < 0` — impossíveis pelo invariante do semáforo; **envolve** a região insegura |

**Semáforo como proteção:** P/V criam região proibida (s < 0 — infeasível) que envolve completamente a região insegura. Nenhuma trajetória válida pode tocar a região insegura.

```c
// Usando semáforo para proteger cnt:
volatile long cnt = 0;
sem_t mutex;
Sem_init(&mutex, 0, 1);

// Em cada thread:
for (i = 0; i < niters; i++) {
    P(&mutex);
    cnt++;    // seção crítica
    V(&mutex);
}
```

> [!note] Limitação do grafo de progresso
> Grafos de progresso descrevem bem uniprocessadores. Em multiprocessadores (CPU/cache pairs com memória compartilhada), o sistema pode estar em estados que o grafo não representa. Regra permanece: **sempre sincronize acessos a variáveis compartilhadas**.

---

## Mutex (semáforo binário)

Semáforo restrito aos valores 0 e 1. Usado para **exclusão mútua**: garante que apenas um processo por vez execute a seção crítica.

```
pthread_mutex_lock(&m)    // down — bloqueia se já travado
  [seção crítica]
pthread_mutex_unlock(&m)  // up — libera um processo bloqueado
```

Mutexes são adequados para **proteções de curto prazo** (acesso a variável compartilhada). Para esperas longas, usar variáveis de condição.

---

## Variáveis de condição (pthreads)

Complemento ao mutex para **esperas longas** (ex: aguardar unidade de fita):

| Chamada | Significado |
|---|---|
| `pthread_cond_wait(&c, &m)` | Suspende e **libera o mutex** atomicamente; reaadquire ao despertar |
| `pthread_cond_signal(&c)` | Libera **exatamente um** thread em espera; se nenhum, sinal é **perdido** |
| `pthread_cond_broadcast(&c)` | Libera **todos** os threads em espera |

> [!note] Diferença de semáforo
> Semáforos contam sinais — um up para semáforo com valor 0 é armazenado para futuro down. Um `pthread_cond_signal` sem nenhum waiter é **descartado**.

---

## Produtor-consumidor com buffer limitado (sbuf)

Padrão CS:APP para buffer bounded com semáforos:

```c
typedef struct {
    int *buf;      // array de itens
    int n;         // capacidade máxima
    int front;     // índice do primeiro item
    int rear;      // índice do último item
    sem_t mutex;   // exclusão mútua para acessar buf
    sem_t slots;   // semáforo de contagem: vagas livres
    sem_t items;   // semáforo de contagem: itens disponíveis
} sbuf_t;

// Inserção (produtor):
void sbuf_insert(sbuf_t *sp, int item) {
    P(&sp->slots);                              // espera vaga livre
    P(&sp->mutex);                              // trava buffer
    sp->buf[(++sp->rear) % sp->n] = item;
    V(&sp->mutex);                              // destrava
    V(&sp->items);                              // sinaliza item disponível
}

// Remoção (consumidor):
int sbuf_remove(sbuf_t *sp) {
    int item;
    P(&sp->items);                              // espera item disponível
    P(&sp->mutex);                              // trava buffer
    item = sp->buf[(++sp->front) % sp->n];
    V(&sp->mutex);                              // destrava
    V(&sp->slots);                              // sinaliza vaga liberada
    return item;
}
```

**Prethreading:** servidor cria pool de worker threads antes de receber requisições. Main thread insere `connfd` no sbuf; workers removem e processam. Evita overhead de criar nova thread por requisição.

---

## Problema leitores-escritores

Threads acessam objeto compartilhado: **leitores** só leem; **escritores** modificam. Escritores requerem acesso exclusivo; leitores podem compartilhar.

**1ª variante — favorece leitores** (nenhum leitor espera desnecessariamente):

```c
int readcnt = 0;         // contagem de leitores na seção crítica
sem_t mutex, w;          // mutex: protege readcnt; w: controla escritores

void reader(void) {
    P(&mutex);
    readcnt++;
    if (readcnt == 1) P(&w);  // primeiro leitor trava escritores
    V(&mutex);
    /* leitura */
    P(&mutex);
    readcnt--;
    if (readcnt == 0) V(&w);  // último leitor libera escritores
    V(&mutex);
}

void writer(void) {
    P(&w);     // exclusão mútua total
    /* escrita */
    V(&w);
}
```

**2ª variante — favorece escritores:** escritor pronto escreve assim que possível; leitores que chegam após escritor esperando devem aguardar.

> [!warning] Starvation
> A 1ª variante pode causar *starvation* de escritores: stream contínua de leitores impede escritor de entrar. A 2ª variante pode causar *starvation* de leitores.

---

## APIs de sincronização

### UNIX / POSIX (pthreads)

| Chamada | Significado |
|---|---|
| `pthread_mutex_init/destroy` | Cria/destrói mutex |
| `pthread_mutex_lock/unlock` | Trava/destrava mutex |
| `pthread_cond_init/destroy` | Cria/destrói variável de condição |
| `pthread_cond_wait/signal` | Espera / libera thread |
| `sem_init/wait/post/destroy` | Semáforo POSIX (contagem arbitrária) |

### Windows 7

| Função | Equivalente UNIX | Significado |
|---|---|---|
| `CreateSemaphore` | `sem_init` | Cria semáforo com valor inicial e máximo |
| `WaitForSingleObject` | `sem_wait` / `down` | Decrementa ou bloqueia (com timeout opcional) |
| `ReleaseSemaphore` | `sem_post` / `up` | Incrementa; libera thread bloqueado |
| `CreateMutex` / `ReleaseMutex` | `pthread_mutex` | Mutex de núcleo (pode ser passado entre processos) |
| `EnterCriticalSection` / `LeaveCriticalSection` | `pthread_mutex_lock` | Seção crítica (espaço do usuário — mais rápida) |
| `CreateEvent` / `SetEvent` / `PulseEvent` | `pthread_cond` | Evento de sincronização |

> [!info] Seções críticas vs. mutexes no Windows
> Seções críticas executam inteiramente em espaço do usuário — não envolvem o núcleo. Por isso são significativamente mais rápidas para proteções dentro de um único processo. Mutexes cruzam para o núcleo e podem ser compartilhados entre processos via duplicação de handle.

---

## Ver também
- [[Processo]] — criação de processos, fork/exec, IPC, threads
- [[Thread]] — thread safety; reentrância; races; deadlocks; uso de semáforos em Pthreads
- [[Multiplexação de IO]] — alternativa ao uso de threads para concorrência
- [[Sistema Operacional]] — semáforos como primitivas do nível OSM
- [[Interrupção]] — desabilitar interrupções: base da implementação de semáforos em uniprocessadores
- [[Condições de Disputa]] — race condition, região crítica, exclusão mútua, quatro condições de Dijkstra
- [[Concorrência e Paralelismo]] — paralelismo real vs. simulado; condições de corrida em multicore
- [[DMA]] — roubo de ciclo como outra forma de acesso concorrente a recursos compartilhados
