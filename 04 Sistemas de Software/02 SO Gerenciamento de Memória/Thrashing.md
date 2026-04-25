---
title: Thrashing
aliases:
  - hiperpaginacao
  - trashing
tags:
  - computação/sistemas
  - sistemas-operacionais/memória
source: "Sistemas Operacionais Modernos, cap. 3"
created_by: Codex
---

# Thrashing

Nota criada pelo Codex.

## Definição

Estado em que o sistema passa tempo demais tratando [[Falha de Página|falhas de página]] e movimentando páginas entre disco e RAM, em vez de executar trabalho útil.

## Causa técnica

Thrashing ocorre quando a soma dos conjuntos de trabalho dos processos ativos excede a [[Memória]] física disponível. Aumentar multiprogramação nesse estado piora o desempenho, porque mais processos competem por quadros insuficientes.

## Funcionamento

**Ciclo de thrashing:**

1. Grau de multiprogramação aumenta → mais processos competem por quadros.
2. Cada processo recebe poucos quadros → insuficientes para seu [[Conjunto de Trabalho]].
3. Processos geram muitas [[Falha de Página|falhas de página]] → ficam bloqueados esperando E/S de disco.
4. SO detecta CPU ociosa → escalona mais processos → piora a situação.
5. Sistema passa mais tempo movendo páginas entre disco e RAM do que executando código útil.

**Controle de carga:** solução principal. SO detecta thrashing monitorando a taxa de falhas de página (ou usando modelo de conjunto de trabalho) e **remove um processo inteiro** da memória (swap out completo), redistribuindo seus quadros. Reduzir o grau de multiprogramação alivia a pressão.

**Detecção por conjunto de trabalho:** soma dos |W(k,t)| de todos os processos. Se ∑|W| > quadros disponíveis → sistema está ou entrará em thrashing. SO interrompe novas admissões ou faz swap out preventivo.

## Relações (SPO)

- Thrashing -> reduz -> vazão do sistema
- Thrashing -> resulta de -> falta de quadros físicos
- Thrashing -> envolve -> [[Troca de Processos]]
- [[Conjunto de Trabalho]] -> permite detectar -> risco de Thrashing
- controle de carga -> reduz -> Thrashing

