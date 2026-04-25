---
title: Inanição
aliases:
  - starvation
  - livelock
tags:
  - computação/sistemas
  - sistemas-operacionais/impasses
source: "Sistemas Operacionais Modernos, cap. 6"
---

# Inanição

**Inanição** (starvation): "Em um sistema dinâmico, solicitações para recursos acontecem o tempo todo. Alguma política é necessária para tomar uma decisão sobre quem recebe qual recurso e quando. Essa política, embora aparentemente razoável, pode levar a alguns processos nunca serem servidos, embora não estejam em situação de impasse." (§6.7.4)

"Um problema relacionado de muito perto com o impasse e o livelock é a inanição."

## Exemplo do livro

Impressora alocada ao processo com o **menor arquivo para imprimir**. "Essa abordagem maximiza o número de clientes felizes e parece justa." Mas: processo com arquivo enorme nunca consegue a impressora em um sistema ocupado, pois sempre chegam processos com arquivos menores.

- Processo não está bloqueado esperando outro processo específico.
- Não há ciclo de dependência — está bloqueado por política de alocação que o preterece indefinidamente.

## Diferença de Impasse e Livelock

| | Impasse | Livelock | Inanição |
|---|---|---|---|
| Processos bloqueados? | Sim (circulamente) | Não (em movimento) | Não necessariamente |
| Progresso possível? | Não | Não (loop ativo) | Sim para outros |
| Causa | Dependência circular | Reação mútua infinita | Política de alocação injusta |

## Solução

Política de **envelhecimento** (aging): quanto mais tempo um processo espera, maior sua prioridade. Eventualmente supera processos novos.

## Relações (SPO)

- Inanição -> diferente de -> [[Impasse]]
- Inanição -> causada por -> política de alocação injusta
- aging -> resolve -> Inanição
- Inanição -> pode ocorrer mesmo sem -> [[Impasse]]
