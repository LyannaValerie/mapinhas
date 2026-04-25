---
title: Evitar Impasses
aliases:
  - deadlock avoidance
  - evitação de deadlock
  - estado seguro
tags:
  - computação/sistemas
  - sistemas-operacionais/impasses
source: "Sistemas Operacionais Modernos, cap. 6"
---

# Evitar Impasses

Estratégia que **aloca recursos dinamicamente** de forma cuidadosa para nunca entrar em estado inseguro. Diferente de [[Prevenção de Impasses]] (que nega condições estruturalmente), evitação toma decisões em tempo de execução.

## Estado Seguro — §6.5.2

"Diz-se de um estado que ele é seguro se existir alguma ordem de escalonamento na qual todos os processos puderem ser executados até sua conclusão mesmo que todos eles subitamente solicitem seu número máximo de recursos imediatamente."

*Exemplo do livro* (10 instâncias totais, 3 disponíveis):
- A: possui 3, pode precisar de até 9.
- B: possui 2, pode precisar de até 4.
- C: possui 2, pode precisar de até 7.

Estado é seguro porque existe a sequência B→A→C: com 3 disponíveis, B termina e devolve 2 (total 5); A pode terminar e devolver (total 8); C pode terminar. "a partir de um estado inseguro, nenhuma garantia nesse sentido pode ser dada."

- Estado seguro → impasse **impossível**
- Estado inseguro → impasse **possível** (não obrigatório — cliente pode não precisar do máximo)

## Trajetórias de Recursos — §6.5.1

Representação 2D: cada eixo = execução de um processo. Regiões onde ambos precisam do mesmo recurso simultaneamente = zonas inseguras. SO deve navegar ao redor dessas regiões.

- Funciona visualmente para dois processos; não escala.
- Conceito analítico — algoritmo prático é o [[Algoritmo do Banqueiro]].

## Limitações Práticas

- Processos raramente declaram necessidades máximas antecipadamente.
- Número de processos não é fixo.
- Recursos podem desaparecer (ex.: unidades de fita quebram).
- **Resultado**: "poucos — se algum — sistemas existentes usam o algoritmo do banqueiro para evitar impasses." Alguns usam heurísticas similares — ex.: redes regulam tráfego ao atingir 70% de buffer, estimando que os 30% restantes bastam para os usuários atuais terminarem.

## Relações (SPO)

- Evitar Impasses -> exige -> declaração antecipada de necessidades
- Evitar Impasses -> mantém sistema em -> estado seguro
- estado seguro -> garante -> ausência de [[Impasse]]
- [[Algoritmo do Banqueiro]] -> implementa -> Evitar Impasses
- Evitar Impasses -> contrasta com -> [[Prevenção de Impasses]]
- Evitar Impasses -> contrasta com -> [[Detecção de Impasses]]
