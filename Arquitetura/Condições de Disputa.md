---
title: Condições de Disputa
aliases:
  - race condition
  - condição de corrida
  - região crítica
  - exclusão mútua
  - mutual exclusion
tags:
  - computação/arquitetura
  - concorrência
  - sistemas-operacionais
date: 2026-04-21
---

# Condições de Disputa

## Definição

Situação em que dois ou mais processos acessam e manipulam **dados compartilhados concorrentemente**, e o resultado final depende da ordem exata de execução — não determinística. O resultado correto exige que apenas um processo por vez acesse os dados compartilhados.

## Relações (SPO)

| Sujeito | Predicado | Objeto |
|---|---|---|
| Condição de disputa | ocorre quando | múltiplos processos acessam dado compartilhado sem sincronização |
| Região crítica | é trecho de código | onde dado compartilhado é acessado |
| Exclusão mútua | previne | condição de disputa na região crítica |
| [[Semáforo]] | implementa | exclusão mútua |
| Busy waiting | é solução de | exclusão mútua (ineficiente) |

## Exemplo: produtor-consumidor

Buffer circular compartilhado entre processo produtor e consumidor. Variável `count` rastreia itens no buffer:

```c
// Produtor                  // Consumidor
while (count == N) sleep();  while (count == 0) sleep();
buffer[in] = item;           item = buffer[out];
in = (in + 1) % N;          out = (out + 1) % N;
count++;                     count--;
if (count == 1) wakeup(C);  if (count == N-1) wakeup(P);
```

**Problema:** `count++` e `count--` não são atômicos. Em assembly:
1. Carrega `count` em registrador
2. Incrementa/decrementa
3. Armazena resultado

Se o escalonador interrompe entre passos 1 e 3, o outro processo vê valor desatualizado → **condição de disputa**.

## Região crítica

Seção do programa onde dado compartilhado é acessado. Para prevenir condições de disputa, a execução das regiões críticas de processos concorrentes deve ser **mutuamente exclusiva no tempo**.

### Quatro condições para exclusão mútua correta (Dijkstra)

1. **Exclusão mútua**: dois processos nunca entram simultaneamente em suas regiões críticas
2. **Sem suposição de velocidade ou número de CPUs**: nenhuma suposição sobre hardware
3. **Sem bloqueio por processo fora da região crítica**: processo fora da região crítica não bloqueia outros
4. **Sem espera indefinida**: nenhum processo espera para sempre para entrar na região crítica

## Soluções

| Solução | Mecanismo | Problema |
|---|---|---|
| Busy waiting (spin lock) | Loop ativo verificando flag | Desperdiça CPU |
| Desabilitar interrupções | Evita troca de contexto | Não funciona em multiprocessador |
| [[Semáforo]] | Operações `up`/`down` atômicas | Solução correta e eficiente |
| Mutex (semáforo binário) | Semáforo restrito a 0/1 | Caso especial de semáforo |
| Monitor | Abstração de alto nível com condição | Linguagem/runtime necessário |

### Busy waiting — Alternância estrita

```c
// Processo 0            // Processo 1
while (turn != 0);      while (turn != 1);
/* região crítica */    /* região crítica */
turn = 1;               turn = 0;
```

Viola condição 3: se P0 está fora da região crítica e turn=0, P1 fica bloqueado.

## Relação com semáforos

[[Semáforo]] (Dijkstra, 1965) resolve condições de disputa sem busy waiting:

- `down(s)`: se s > 0, decrementa; senão bloqueia o processo
- `up(s)`: incrementa s; se havia processo bloqueado, acorda-o

Operações são **atômicas** — implementadas via instrução especial da CPU (test-and-set, compare-and-swap) ou desabilitando interrupções brevemente no núcleo.

> [!warning] Deadlock
> Uso incorreto de semáforos pode causar **deadlock**: dois processos bloqueados esperando um pelo outro indefinidamente.

## Ver também

- [[Semáforo]] — mecanismo principal de solução
- [[Concorrência e Paralelismo]] — contexto mais amplo
- [[Chamada do Sistema]] — `fork`, `wait`, criação de processos
