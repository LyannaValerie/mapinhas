---
title: Impasse
aliases:
  - deadlock
  - impasses
tags:
  - computação/sistemas
  - sistemas-operacionais/impasses
source: "Sistemas Operacionais Modernos, cap. 6"
---

# Impasse

**Definição formal** (§6.2): "Um conjunto de processos estará em situação de impasse se cada processo no conjunto estiver esperando por um evento que apenas outro processo no conjunto pode causar."

**Impasse de recurso** — o tipo mais comum: "Nenhum dos processos pode executar, nenhum deles pode liberar quaisquer recursos e nenhum pode ser desperto." O número de processos e o tipo de recursos não têm importância — o resultado é válido para hardware e software.

Ocorrem principalmente com [[Recursos Preemptíveis e Não Preemptíveis|recursos não preemptíveis]] porque impasses que envolvem recursos preemptíveis normalmente se resolvem por realocação.

## Condições necessárias

Todas as quatro [[Condições de Coffman]] devem estar presentes simultaneamente:

1. **Exclusão mútua** — recurso alocado a no máximo um processo por vez.
2. **Posse e espera** — processo detém recurso e aguarda outro.
3. **Não preempção** — recurso não pode ser tomado à força.
4. **Espera circular** — cadeia circular de processos, cada um esperando o próximo.

## Quatro estratégias para lidar com impasses

| Estratégia | Descrição |
|---|---|
| [[Algoritmo do Avestruz]] | Ignorar o problema |
| [[Detecção de Impasses]] | Deixar ocorrer, detectar e recuperar |
| [[Evitar Impasses]] | Alocação dinâmica cuidadosa (estado seguro) |
| [[Prevenção de Impasses]] | Negar estruturalmente uma das condições |

## Relações (SPO)

- Impasse -> bloqueia -> conjunto de processos permanentemente
- Impasse -> exige -> todas as [[Condições de Coffman]] simultaneamente
- Impasse -> ocorre com -> [[Recursos Preemptíveis e Não Preemptíveis|recursos não preemptíveis]]
- [[Grafo de Alocação de Recursos]] -> representa -> Impasse
- [[Detecção de Impasses]] -> identifica e recupera -> Impasse
- [[Prevenção de Impasses]] -> elimina estruturalmente -> Impasse
- [[Evitar Impasses]] -> previne dinamicamente -> Impasse
- [[Inanição]] -> diferente de -> Impasse

