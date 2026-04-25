---
title: Algoritmo do Avestruz
aliases:
  - ostrich algorithm
  - ignorar impasse
tags:
  - computação/sistemas
  - sistemas-operacionais/impasses
source: "Sistemas Operacionais Modernos, cap. 6"
---

# Algoritmo do Avestruz

Estratégia mais simples para lidar com [[Impasse|impasses]]: **enfiar a cabeça na areia e fingir que não há problema**.

## Fundamento — §6.3

"As pessoas reagem a essa estratégia de maneiras diferentes. Matemáticos a consideram inaceitável e dizem que os impasses devem ser evitados a todo custo. Engenheiros perguntam qual a frequência que se espera que o problema ocorra, qual a frequência com que ocorrem quedas no sistema por outras razões e quão sério um impasse é realmente."

"Se os impasses ocorrem na média uma vez a cada cinco anos, mas quedas do sistema decorrentes de falhas no hardware e defeitos no sistema operacional ocorrem uma vez por semana, a maioria dos engenheiros não estaria disposta a pagar um alto preço em termos de desempenho ou conveniência para eliminar os impasses."

Exemplo concreto: SO que bloqueia o chamador numa chamada de sistema `open` para dispositivo físico (Blu-ray). Se vários processos abrirem dispositivos simultâneos em ordem diferente → impasse. Custo de eliminar esse impasse via restrições é alto.

"A maioria dos sistemas operacionais, incluindo UNIX e Windows, na essência apenas ignora o problema presumindo que a maioria dos usuários preferiria um livelock ocasional (ou mesmo um impasse) a uma regra restringindo todos os usuários a um processo, um arquivo aberto e um de tudo."

"Estamos diante de uma escolha desagradável entre conveniência e correção, e muita discussão sobre o que é mais importante, e para quem."

## Relações (SPO)

- Algoritmo do Avestruz -> ignora -> [[Impasse]]
- Algoritmo do Avestruz -> contrasta com -> [[Detecção de Impasses]]
- Algoritmo do Avestruz -> contrasta com -> [[Prevenção de Impasses]]
- Algoritmo do Avestruz -> contrasta com -> [[Evitar Impasses]]
- UNIX -> usa -> Algoritmo do Avestruz
