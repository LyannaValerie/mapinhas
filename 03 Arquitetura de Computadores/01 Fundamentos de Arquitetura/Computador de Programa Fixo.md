---
title: Computador de Programa Fixo
aliases:
  - computador controlado por programa
  - programa fixo
tags:
  - computação/arquitetura
  - computação/histórico
date: 2026-04-06
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Computador de Programa Fixo

## Definição
Modelo anterior ao [[Modelo de Von Neumann]] no qual o [[Processador]] era vinculado a um único programa. Reprogramar o computador para outra tarefa exigia mover fisicamente cabos e componentes eletrônicos — ou seja, a programação era feita por [[Hardware]].

## Relações (SPO)
- Computador de Programa Fixo → precedeu → [[Modelo de Von Neumann]]
- Computador de Programa Fixo → programado via → [[Hardware]]
- Computador de Programa Fixo → substituído por → [[Programa Armazenado]]

## Contraste com Von Neumann

> [!tip] Diferença fundamental
> No modelo de programa fixo, mudar de tarefa = mexer no hardware fisicamente.
> No [[Modelo de Von Neumann]], mudar de tarefa = carregar outro [[Programa Armazenado]] na memória.

## Exemplos atuais
Calculadoras de bolso **não programáveis** são um exemplo contemporâneo deste modelo: executam sempre as mesmas operações fixas, sem possibilidade de reprogramação via software.

## Babbage vs. CD-ROM — são a mesma coisa? (Q8)

À primeira vista, a Máquina Diferencial de Babbage (programa fixo no hardware) e um CD-ROM moderno (que não pode ser alterado) parecem análogos. Mas há uma diferença fundamental:

| Característica | Babbage | CD-ROM |
|---|---|---|
| O que é fixo | A **computação** — a máquina só faz uma coisa | Os **dados** — o conteúdo gravado |
| Quem executa | A própria máquina (hardware = programa) | Qualquer computador com leitor de CD |
| Flexibilidade | Nenhuma — mudar a tarefa exige reconstruir a máquina | Total — o mesmo CD pode ser lido por qualquer programa (player, ripador, etc.) |
| Analogia correta | Hardware **é** o programa | Dado imutável, mas **independente** do programa que o processa |

> [!note] Conclusão
> **Não são essencialmente a mesma coisa.** No modelo de Babbage, hardware e programa são inseparáveis. Um CD-ROM é apenas armazenamento de dados imutável — o programa que decide *o que fazer* com esses dados reside no [[Software]] do computador que o lê, podendo ser qualquer coisa. Um CD-ROM de música pode ser reproduzido, copiado, analisado espectralmente ou convertido para MP3 — tudo sem tocar no disco.

## Ver também
- [[Modelo de Von Neumann]]
- [[Programa Armazenado]]
- [[Hardware]]
- [[Processador]]
