---
title: Transistor
aliases:
  - transistores
  - transistor bipolar
  - BJT
  - FET
  - MOSFET
tags:
  - computação/hardware
  - computação/fundamentos
date: 2026-04-18
---

# Transistor

## Definição
Componente semicondutor que funciona como **interruptor eletrônico controlável** ou amplificador de sinal. É o bloco construtivo fundamental de todos os [[Circuito Integrado|circuitos integrados]] modernos — [[Processador|processadores]], memórias e periféricos são compostos por bilhões de transistores.

Inventado no **Bell Labs em 1948** por **John Bardeen, Walter Brattain e William Shockley** (Prêmio Nobel de Física, 1956). Substituiu as válvulas termiônicas ao final da década de 1950.

## Relações (SPO)
- Transistor → inventado por → Bardeen, Brattain e Shockley (Bell Labs, 1948)
- Transistor → substitui → válvula termiônica (tubo de vácuo)
- Transistor → compõe → [[Circuito Integrado]]
- Transistor → implementa → [[Portas Lógicas]] (AND, OR, NOT...)
- Transistor → quantidade segue → [[Lei de Moore]] (dobra a cada ~2 anos)
- MOSFET → variante dominante em → [[Fabricação de Chips|chips CMOS]] modernos

## Papel na computação

| Uso | Como o transistor age |
|---|---|
| Porta lógica | Interruptor: ligado = 1, desligado = 0 |
| Célula SRAM | Par de transistores cruzados retendo bit |
| Célula DRAM | Transistor + capacitor armazenando carga |
| Célula Flash/NAND | Transistor com gate flutuante retendo carga sem alimentação |

## Evolução e Lei de Moore
A redução contínua do tamanho dos transistores (em nanômetros — nm) permite colocar mais deles por chip, aumentando desempenho e reduzindo consumo. Ver [[Lei de Moore]].

Limites físicos emergentes:
- Efeitos quânticos em nós abaixo de ~5 nm
- Transistores de átomo único tornam-se não confiáveis

## Segunda Geração de Computadores
A substituição das válvulas por transistores (1955–1965) definiu a **segunda geração** de computadores: máquinas menores, mais rápidas, mais confiáveis e com menor consumo de energia. Ver [[História da Computação]].

## Ver também
- [[Circuito Integrado]]
- [[Portas Lógicas]]
- [[Fabricação de Chips]]
- [[Lei de Moore]]
- [[História da Computação]]
- [[Memória Flash]]
