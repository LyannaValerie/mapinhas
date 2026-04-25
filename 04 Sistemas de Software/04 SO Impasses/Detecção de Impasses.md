---
title: Detecção de Impasses
aliases:
  - detecção de deadlocks
  - deadlock detection
tags:
  - computação/sistemas
  - sistemas-operacionais/impasses
source: "Sistemas Operacionais Modernos, cap. 6"
---

# Detecção de Impasses

Estratégia que **permite impasses ocorrerem** e executa algoritmos periódicos para identificar ciclos ou estados bloqueados, seguidos de recuperação. Contrasta com [[Prevenção de Impasses]] (estrutural) e [[Evitar Impasses]] (dinâmico preventivo).

## Quando executar o algoritmo

- A cada solicitação de recurso: custo alto, mas detecta imediatamente.
- Periodicamente (ex.: a cada hora, ou quando **utilização da CPU cai abaixo de um limiar** — indica que processos em impasse consomem slots sem trabalho real).

## Algoritmos de detecção

### Um recurso de cada tipo — §6.4.1
Usar [[Grafo de Alocação de Recursos]] e buscar ciclos com o algoritmo de DFS. Ciclo = impasse confirmado.

### Múltiplos recursos de cada tipo — §6.4.2
Algoritmo baseado em matrizes para n processos e m classes de recursos:
- **E**: vetor de recursos existentes (`E_i` = total da classe i).
- **A**: recursos disponíveis (`A = E − soma das colunas de C`).
- **C**: matriz de alocação atual (`C_ij` = quanto do recurso j o processo i possui).
- **R**: matriz de requisições pendentes (`R_ij` = quanto do recurso j o processo i ainda precisa).

Algoritmo:
1. Buscar processo i não marcado onde `R_i ≤ A` (requisições satisfazíveis com o disponível).
2. Simular término: `A += C_i`; marcar i como terminado.
3. Repetir até não encontrar candidato.
4. Processo não marcado ao final → **em impasse**.

## Recuperação — §6.4.3

### Mediante preempção
Tomar temporariamente o recurso do processo-proprietário e entregá-lo a outro. *Exemplo do livro*: para tomar uma impressora a laser, o operador junta as folhas já impressas em uma pilha, suspende o processo (marca como não executável), aloca a impressora para outro processo. Quando esse termina, as folhas são devolvidas à bandeja e o processo original reinicializado. "A recuperação dessa maneira é com frequência difícil ou impossível."

### Mediante retrocesso (rollback)
Processos geram **pontos de salvaguarda (checkpoints)** periodicamente — capturam estado completo em disco. Ao detectar impasse, reverter processo ao checkpoint anterior à aquisição do recurso disputado e recomeçar. O recurso é liberado para outro processo.

### Mediante eliminação de processos
Matar um ou mais processos do ciclo de impasse. Escolher processos que possam ser re-executados sem efeitos colaterais externos (preferência). Solução mais grosseira mas sempre funciona.

## Relações (SPO)

- Detecção de Impasses -> identifica -> [[Impasse]]
- Detecção de Impasses -> usa -> [[Grafo de Alocação de Recursos]]
- Detecção de Impasses -> exige -> política de recuperação
- recuperação por retrocesso -> usa -> checkpoint
- Detecção de Impasses -> contrasta com -> [[Prevenção de Impasses]]
- Detecção de Impasses -> contrasta com -> [[Evitar Impasses]]

