---
title: Thread
aliases:
  - threads
  - thread POSIX
  - Pthreads
  - pthread
  - thread safety
  - thread-safe
  - reentrant
  - reentrância
  - race condition
  - corrida de threads
  - deadlock
  - TID
  - joinable
  - detached
tags:
  - computação/arquitetura
date: 2026-04-14
source: "CS:APP - Cap. 12"
---

# Thread

## Definição
Fluxo lógico de controle que executa no contexto de um [[Processo]]. Múltiplas threads rodam concorrentemente em um único processo, compartilhando o mesmo espaço de endereçamento virtual.

## Relações (SPO)
- Thread → executa no contexto de → [[Processo]]
- Thread → identificada por → TID (Thread ID) inteiro
- Thread → compartilha com pares → espaço de endereçamento virtual (código, dados, heap, libs, arquivos abertos)
- Thread → possui exclusivamente → contexto de thread (TID, pilha, SP, PC, registradores, condition codes)
- Thread → é manipulada via → Pthreads (POSIX Threads)
- Thread → context switch → mais rápido que troca de contexto de processo
- Thread segura → contém → funções thread-safe
- Função reentrante → é subconjunto de → funções thread-safe
- Deadlock → causado por → aquisição desordenada de semáforos/mutexes
- Race condition → causado por → suposição incorreta sobre ordem de escalonamento

---

## Modelo de execução

Cada processo começa com uma **thread principal** (*main thread*). A main thread cria **threads pares** (*peer threads*); a partir daí rodam concorrentemente.

```
Tempo →
Thread 1 (main) ───────┤ ctx switch ├────────────────────┤ ctx switch ├─────
Thread 2 (peer) ───────────────────────┤ ctx switch ├────────────────────────
```

**Diferenças fundamentais de processos:**
- Troca de contexto de thread é mais rápida (contexto menor)
- Threads formam **pool de pares** — não hierarquia pai/filho rígida
- Qualquer thread pode matar ou esperar qualquer par
- Todos os pares podem ler/escrever os mesmos dados compartilhados

---

## Modelo de memória de threads

| Componente | Propriedade |
|---|---|
| Registradores | **Nunca compartilhados** — cada thread tem os seus |
| Pilha (*stack*) | Geralmente independente, mas **não protegida** de outras threads |
| Memória virtual (código, dados, heap, libs) | **Sempre compartilhada** entre todas as threads do processo |
| Arquivos abertos | **Compartilhados** |

> [!warning] Pilhas não são protegidas
> Threads diferentes podem acessar a pilha umas das outras se obtiverem um ponteiro para ela. Variáveis automáticas de uma thread podem ser referenciadas por outra via ponteiro global.

### Mapeamento de variáveis C para memória

| Classe | Instâncias | Compartilhada? |
|---|---|---|
| Global (declarada fora de função) | **Uma** na área read/write | Sim |
| Local automática (sem `static`) | **Uma por thread** (na pilha de cada) | Não (exceto via ponteiro) |
| Local estática (`static` dentro de função) | **Uma** na área read/write | Sim |

---

## API Pthreads

```c
#include <pthread.h>
```

### Criação e identificação

| Função | Descrição |
|---|---|
| `pthread_create(tid, attr, func, arg)` | Cria thread que executa `func(arg)`; `tid` recebe TID |
| `pthread_self()` | Retorna TID da thread chamadora |

```c
typedef void *(func)(void *);
int pthread_create(pthread_t *tid, pthread_attr_t *attr, func *f, void *arg);
// Returns: 0 se OK, nonzero em erro
```

### Terminação

| Forma | Descrição |
|---|---|
| Retorno da rotina | Encerra a thread implicitamente |
| `pthread_exit(retval)` | Encerra explicitamente; se main thread chama, espera todos os pares |
| `exit()` | Encerra **todo o processo** e todas as threads |
| `pthread_cancel(tid)` | Outro par cancela a thread `tid` |

### Sincronização e ciclo de vida

| Função | Descrição |
|---|---|
| `pthread_join(tid, retval)` | Bloqueia até `tid` terminar; colhe recursos; recebe valor de retorno |
| `pthread_detach(tid)` | Desanexa thread — recursos liberados automaticamente ao terminar |
| `pthread_once(once_ctrl, init_fn)` | Garante que `init_fn` é chamada **apenas uma vez**, mesmo com múltiplas threads |

### Estados de uma thread

| Estado | Comportamento |
|---|---|
| **Joinable** (padrão) | Deve ser coletada por `pthread_join` ou desanexada; caso contrário, vaza memória |
| **Detached** | Recursos liberados automaticamente ao terminar; não pode ser coletada nem cancelada |

> [!tip] Padrão recomendado para servidores
> Threads que tratam requisições independentes devem chamar `pthread_detach(pthread_self())` no início da rotina, evitando necessidade de gerenciamento explícito.

### Armadilha: passar argumento para thread

```c
// ERRADO — race entre main thread e peer thread
connfd = Accept(...);
pthread_create(&tid, NULL, thread, &connfd); // ponteiro para variável local!

// CORRETO — cada conexão tem seu próprio bloco de memória
connfdp = Malloc(sizeof(int));
*connfdp = Accept(...);
pthread_create(&tid, NULL, thread, connfdp);
// thread deve Free(vargp) internamente
```

---

## Thread safety

Função **thread-safe**: produz resultados corretos quando chamada repetidamente por múltiplas threads concorrentes.

### 4 classes de funções thread-unsafe

| Classe | Problema | Correção |
|---|---|---|
| **1** | Não protege variáveis compartilhadas (ex: contador global) | Envolver acessos com P/V (mutex) |
| **2** | Mantém estado entre chamadas (ex: `rand` — usa `static next_seed`) | Reescrever como reentrante (passar estado via argumento) |
| **3** | Retorna ponteiro para variável estática (ex: `ctime`, `gethostbyname`) | Técnica lock-and-copy |
| **4** | Chama função thread-unsafe | Depende da classe da função chamada |

#### Técnica lock-and-copy (classe 3)

```c
char *ctime_ts(const time_t *timep, char *privatep) {
    char *sharedp;
    P(&mutex);
    sharedp = ctime(timep);         // chama função thread-unsafe
    strcpy(privatep, sharedp);      // copia resultado para buffer privado
    V(&mutex);
    return privatep;
}
```

### Funções thread-unsafe comuns da libc Linux

| Função unsafe | Classe | Versão reentrante |
|---|---|---|
| `rand` | 2 | `rand_r` |
| `strtok` | 2 | `strtok_r` |
| `ctime` | 3 | `ctime_r` |
| `asctime` | 3 | `asctime_r` |
| `gethostbyname` | 3 | `gethostbyname_r` |
| `localtime` | 3 | `localtime_r` |

Regra: funções com sufixo `_r` são versões reentrantes das originais.

---

## Reentrância

Função **reentrante**: não referencia nenhum dado compartilhado quando chamada por múltiplas threads. Subconjunto próprio das funções thread-safe.

```
Todas as funções
├── Thread-safe
│   ├── Reentrantes       ← sem dados compartilhados; mais eficientes
│   └── Não-reentrantes   ← usam mutex/lock; menor desempenho
└── Thread-unsafe
```

| Tipo | Característica |
|---|---|
| **Explicitamente reentrante** | Todos args por valor + só variáveis automáticas locais |
| **Implicitamente reentrante** | Aceita ponteiros, mas apenas para dados não compartilhados |

```c
// rand thread-unsafe: usa static
unsigned next_seed = 1;
unsigned rand(void) {
    next_seed = next_seed * 1103515245 + 12543;
    return (unsigned)(next_seed >> 16) % 32768;
}

// rand_r reentrante: estado passado pelo chamador
int rand_r(unsigned int *nextp) {
    *nextp = *nextp * 1103515245 + 12345;
    return (unsigned int)(*nextp / 65536) % 32768;
}
```

---

## Races (condições de corrida)

**Race:** correção do programa depende de uma thread atingir ponto X antes de outra atingir ponto Y. Ocorre quando programador assume trajetória específica de escalonamento.

```c
// RACE: main incrementa i antes do peer ler *vargp
for (i = 0; i < N; i++)
    pthread_create(&tid[i], NULL, thread, &i); // passa endereço de i!

// Peer thread:
int myid = *((int *)vargp); // pode ler i já incrementado
```

**Correção:** alocar memória independente para cada argumento.

> [!warning] Races são não-determinísticos
> O programa pode funcionar corretamente em 999 de 1000 execuções. Diferentes máquinas ou cargas de trabalho podem expor o bug. Sempre verifique se argumentos passados para threads têm vida útil garantida.

---

## Deadlocks

**Deadlock:** conjunto de threads bloqueado esperando condição que nunca será verdadeira (espera circular em semáforos).

**Características no grafo de progresso:**
- Regiões proibidas de dois semáforos se sobrepõem
- Sobreposição cria **região de deadlock**
- Trajetórias que entram na região de deadlock nunca saem

> [!danger] Mutex lock ordering rule
> **Regra de ordenação de mutexes:** dado uma ordenação total de todos os mutexes, o programa é livre de deadlock se cada thread adquire seus mutexes em ordem e os libera em ordem reversa.

```c
// DEADLOCK POSSÍVEL — Thread 1: P(s) P(t); Thread 2: P(t) P(s)
// CORRETO — ambas as threads: P(s) depois P(t)
P(&s);
P(&t);
// seção crítica
V(&t);
V(&s);
```

---

## Servidor concorrente baseado em threads

```c
// Main thread: aceita conexões, cria peer threads
while (1) {
    connfdp = Malloc(sizeof(int));
    *connfdp = Accept(listenfd, ...);
    Pthread_create(&tid, NULL, thread, connfdp);
}

// Peer thread: detach + processa + libera
void *thread(void *vargp) {
    int connfd = *((int *)vargp);
    Pthread_detach(pthread_self());
    Free(vargp);
    echo(connfd);
    Close(connfd);
    return NULL;
}
```

---

## Ver também
- [[Processo]] — fork, contexto, IPC; threads como extensão do modelo de processo
- [[Semáforo]] — P/V, mutex, produtor-consumidor, leitores-escritores, grafos de progresso
- [[Concorrência e Paralelismo]] — abordagens de programação concorrente; speedup e eficiência
- [[Multiplexação de IO]] — alternativa baseada em select() para programação concorrente
- [[Sinais]] — mecanismo de IPC leve entre processos
