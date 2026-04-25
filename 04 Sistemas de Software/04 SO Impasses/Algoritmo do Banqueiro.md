---
title: Algoritmo do Banqueiro
aliases:
  - banker's algorithm
  - algoritmo do banqueiro de Dijkstra
tags:
  - computação/sistemas
  - sistemas-operacionais/impasses
source: "Sistemas Operacionais Modernos, cap. 6"
---

# Algoritmo do Banqueiro

Algoritmo de [[Evitar Impasses]] proposto por Dijkstra (1965). "É modelado da maneira pela qual um banqueiro de uma cidade pequena poderia lidar com um grupo de clientes para os quais ele concedeu linhas de crédito." (Anos atrás, bancos não emprestavam sem certeza de ressarcimento.)

## Analogia

- **Clientes** = processos
- **Linhas de crédito / empréstimos** = recursos alocados
- **Crédito máximo declarado** = necessidade máxima do processo
- **Reserva do banqueiro** = recursos disponíveis no sistema

## Algoritmo (recurso único) — §6.5.3

"O que o algoritmo faz é conferir para ver se conceder a solicitação leva a um estado inseguro. Se afirmativo, a solicitação é negada."

"O algoritmo do banqueiro considera cada solicitação à medida que ela ocorre, vendo se concedê-la leva a um estado seguro. Se afirmativo, a solicitação é concedida; de outra maneira, ela é adiada."

Para ver se estado é seguro: banqueiro confere se tem recursos suficientes para satisfazer algum cliente → assume que empréstimos serão ressarcidos → confere o próximo mais próximo do limite → repete. Se todos puderem ser ressarcidos → estado seguro → solicitação concedida.

## Algoritmo (múltiplos recursos) — §6.5.4

Generalização com matrizes (n processos, m classes de recursos):
- **E**: vetor de recursos existentes (`E_i` = total da classe i).
- **A**: recursos disponíveis (`A = E − soma das colunas de C`).
- **C**: alocação atual (`C_ij` = quanto do recurso j o processo i possui).
- **R**: necessidade restante (`R_ij` = quanto do recurso j o processo i ainda precisa).

Teste de estado seguro:
1. Buscar processo i não terminado onde `R_i ≤ A`.
2. Se encontrado: `A += C_i`; marcar como terminado.
3. Repetir até todos terminados (seguro) ou sem candidato (inseguro).

Processos precisam declarar suas necessidades máximas antecipadamente (matrizes C e R).

## Limitações

- Processos raramente sabem antecipadamente suas necessidades máximas — se soubessem, o algoritmo do banqueiro poderia ser usado.
- Número de processos deve ser fixo.
- Recursos não podem desaparecer (ex.: unidade de fita quebrar).
- **Na prática, poucos — se algum — sistemas existentes implementam o algoritmo do banqueiro.**

## Relações (SPO)

- Algoritmo do Banqueiro -> verifica -> estado seguro antes de alocar
- Algoritmo do Banqueiro -> implementa -> [[Evitar Impasses]]
- estado inseguro -> pode levar a -> [[Impasse]]
- Algoritmo do Banqueiro -> exige -> declaração de necessidade máxima
- Algoritmo do Banqueiro -> usa -> [[Grafo de Alocação de Recursos]] (versão matricial)
