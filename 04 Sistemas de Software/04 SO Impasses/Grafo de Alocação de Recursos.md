---
title: Grafo de Alocação de Recursos
aliases:
  - resource allocation graph
  - grafo de recursos
tags:
  - computação/sistemas
  - sistemas-operacionais/impasses
source: "Sistemas Operacionais Modernos, cap. 6"
---

# Grafo de Alocação de Recursos

Representação de processos, recursos, requisições e alocações usada para detectar dependências circulares que formam [[Impasse|impasses]].

## Notação

- **Nó circular** = processo
- **Nó quadrado** = recurso (com pontinhos para instâncias)
- **Arco processo → recurso** = processo solicitando recurso
- **Arco recurso → processo** = recurso alocado ao processo

## Ciclos e impasse

- **Um recurso de cada tipo**: ciclo no grafo ↔ impasse.
- **Múltiplas instâncias por recurso**: ciclo é condição necessária mas não suficiente — pode não haver impasse.

## Algoritmo de detecção (um recurso de cada tipo) — §6.4.1

Para cada nó N no grafo, execute:
1. Inicialize L como lista vazia; designe todos os arcos como desmarcados.
2. Adicione o nó atual ao final de L; confira se ele aparece duas vezes. Se sim → **ciclo = impasse**, termina.
3. A partir do nó atual, verifique se há arco de saída desmarcado. Se sim → passo 4; se não → passo 5.
4. Escolha aleatoriamente um arco de saída desmarcado e marque-o. Siga-o para o novo nó atual e vá ao passo 2.
5. Beco sem saída. Se N é o nó inicial → sem ciclo; tente outro nó. Senão, remova N de L, retorne ao nó anterior e vá ao passo 3.

## Detecção com múltiplos recursos (matricial)

Usa vetores e matrizes:
- **E**: vetor de recursos existentes.
- **A**: recursos disponíveis (A = E − soma de C).
- **C**: matriz de alocação atual.
- **R**: matriz de requisições pendentes.

Buscar linha i tal que R_i ≤ A, marcar como terminado, liberar C_i. Repetir. Processo não marcado ao final → em impasse.

## Relações (SPO)

- Grafo de Alocação de Recursos -> modela -> posse de recursos e requisições
- ciclo no grafo (recurso único) -> indica -> [[Impasse]]
- ciclo no grafo (múltiplas instâncias) -> necessário mas não suficiente para -> [[Impasse]]
- [[Detecção de Impasses]] -> usa -> Grafo de Alocação de Recursos
- Grafo de Alocação de Recursos -> pode ser generalizado para -> múltiplos recursos do mesmo tipo

