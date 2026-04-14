---
title: Multitarefa
aliases:
  - multitasking
  - timeslicing
  - divisão de tempo
  - multitarefa preemptiva
  - multitarefa cooperativa
  - troca de contexto
  - context switch
  - TSS
  - Task-State Segment
  - preemptive multitasking
  - cooperative multitasking
tags:
  - computação/arquitetura
date: 2026-04-09
---

# Multitarefa

## Definição
Técnica que permite ao [[Processador]] executar **um pouco de cada programa carregado por vez**, alternando entre eles em frações de segundo. O próprio [[Sistema Operacional]] é um programa participante deste esquema.

---

## Mecanismo — Divisão de Tempo (Timeslicing)

O processador executa um programa por alguns milissegundos, suspende-o, executa o próximo, e assim por diante em ciclo contínuo:

```
Programa A → [alguns ms] → Programa B → [alguns ms] → Programa C → [alguns ms] → Programa A → ...
```

Como a troca ocorre em frações de segundo imperceptíveis ao usuário, cria-se a **ilusão** de que todos os programas rodam simultaneamente — mesmo com um único núcleo. Esta variante é chamada **multitarefa preemptiva**: o SO interrompe (preempta) o programa a qualquer momento, sem pedir permissão.

## Relações (SPO)
- Multitarefa → usa → divisão de tempo (timeslicing)
- Multitarefa preemptiva → controlada por → SO ou hardware
- TSS → armazena → estado do processador durante troca de tarefa
- Troca de contexto → salva/restaura → registradores e pilha

---

## Multitarefa ≠ Múltiplos Núcleos

> [!warning] Equívoco comum
> Muitos usuários acreditam que um processador de 2 núcleos executa exatamente 2 programas ao mesmo tempo, e 4 núcleos, exatamente 4. Isso está errado.
>
> **Multitarefa** é a técnica de alternar entre programas rapidamente — funciona com qualquer número de núcleos, incluindo **1**. Sem multitarefa, um computador de 4 núcleos só rodaria 4 programas; com ela, roda dezenas.
>
> **Múltiplos núcleos** habilitam **multiprocessamento simétrico** (SMP) — um conceito diferente, que permite execução verdadeiramente paralela. Ver [[Concorrência e Paralelismo#Multi-core]].

---

## Troca de Contexto (Context Switch)

Para que cada programa "pense" que tem o processador só para si, o SO precisa:

1. **Antes de suspender a tarefa atual:** salvar o estado completo do processador — conteúdo de todos os [[Registrador|registradores]] e da [[Pilha]]
2. **Ao retomar uma tarefa:** restaurar o estado salvo anteriormente

Este processo é chamado **troca de contexto**. Sem ele, um programa encontraria os registradores com valores deixados pelo programa anterior.

---

## Controle por Hardware (modo protegido 32 bits)

No modo protegido de 32 bits, o controle de multitarefa pode ser feito por hardware:

### TSS — Task-State Segment

Cada tarefa possui uma tabela chamada **TSS** (Task-State Segment — Segmento de Estado da Tarefa), armazenada na memória, onde o estado completo do processador é salvo a cada troca de contexto.

O registrador **TR** ([[Registradores x86#Registradores de Gerenciamento de Memória|Task Register]]) aponta para o TSS da tarefa corrente.

### Temporizador programável

O processador possui um **temporizador interno programável** pelo SO. Quando o tempo de fatia (time slice) de uma tarefa expira, o temporizador gera uma [[Interrupção|interrupção]], comandando a troca para a próxima tarefa.

> [!note] Modo de 64 bits: sem controle por hardware
> O controle de multitarefa por **hardware foi removido** no modo de 64 bits. O SO é o responsável exclusivo pela troca de contexto. A TSS ainda existe no modo de 64 bits, mas serve apenas para armazenar informações de controle do modo — não para gerenciar a multitarefa.

---

## Multitarefa Cooperativa (histórico)

Usada pelo **Windows 3.x, 95, 98 e ME** ao rodar programas de 16 bits, que não se beneficiavam da proteção de memória:

| Aspecto | Multitarefa cooperativa | Multitarefa preemptiva |
|---|---|---|
| Quem controla a troca | O **próprio programa** (avisa o SO) | O **SO** (interrompe o programa) |
| Confiabilidade | Depende da boa vontade de cada programa | Robusta — SO sempre tem controle |
| Consequência de travamento | Um programa travado bloqueia todos | Programa travado pode ser encerrado pelo SO |
| Usado em | Windows 3.x/95/98/ME (16 bits) | Todos os SOs modernos |

> [!warning] Instabilidade histórica
> A multitarefa cooperativa era a causa da instabilidade notória do Windows 9x: um programa mal-escrito que não cedesse o controle ao SO travava o sistema inteiro.

A multitarefa controlada por software do modo de 64 bits é superior à cooperativa, pois é **controlada pelo SO** — não pelos programas.

---

## Ver também
- [[Concorrência e Paralelismo]] — multicore, hyperthreading, ILP e SIMD
- [[Processador]] — temporizador interno e troca de contexto
- [[Sistema Operacional]] — o SO gerencia a multitarefa preemptiva
- [[Registrador]] — estado salvo na troca de contexto
- [[Registradores x86]] — TR (Task Register) aponta para a TSS
- [[Proteção de Memória x86]] — proteção de memória necessária para multitarefa preemptiva segura
- [[Modos de Operação x86]] — hardware multitasking disponível apenas no modo protegido 32 bits
- [[Interrupção]] — temporizador gera interrupção para forçar troca de tarefa
