---
title: Linguagem Assembly
aliases:
  - assembly
  - linguagem de montagem
  - assembler
  - nível 4
tags:
  - computação/fundamentos
date: 2026-04-06
---

# Linguagem Assembly

## Definição
Nível 4 da [[Níveis de Abstração|organização estruturada de computadores]]. É uma forma **simbólica** das linguagens dos níveis inferiores ([[ISA]], nível 2 ou 3), que substitui sequências numéricas por mnemonics legíveis por humanos. Programas em assembly são traduzidos para o nível 1, 2 ou 3 por um programa chamado **assembler**.

## Relações (SPO)
- Linguagem Assembly → é o nível 4 de → [[Níveis de Abstração]]
- Linguagem Assembly → representa simbolicamente → [[ISA]] (nível 2)
- Linguagem Assembly → traduzida por → assembler
- Linguagem Assembly → mais legível que → [[Linguagem de Máquina]]
- Linguagem Assembly → menos abstrata que → linguagens de alto nível (nível 5)

## Posição na hierarquia

```
Nível 5 → Linguagem de alto nível (C, Python…)   ← compilador
Nível 4 → Linguagem Assembly                      ← assembler  ← AQUI
Nível 3 → Sistema Operacional
Nível 2 → ISA
```

## Características

- Primeira linguagem da hierarquia a usar **palavras e abreviações** (não apenas números)
- Cada instrução assembly corresponde essencialmente a uma instrução de máquina (relação quase 1:1)
- Permite ao programador controlar diretamente [[Registrador|registradores]] e [[Memória]]
- Usada por **programadores de sistemas** (não de aplicações)

> [!tip] Assembly vs. linguagem de máquina
> Assembly é uma representação simbólica legível da [[Linguagem de Máquina]]. O assembler faz a conversão de volta para os números binários que o processador entende.

## Assembler
Programa que realiza a tradução de assembly para linguagem de máquina (nível 1/2/3). A conversão é direta: cada mnemônico corresponde a **um único código numérico** no conjunto de instruções do processador. Pode ser feita:
- **Manualmente** — o programador consulta a tabela do conjunto de instruções e substitui mnemônicos pelos valores.
- **Automaticamente** — usando um programa assembler.

> [!example] Exemplo x86 concreto
> Instrução assembly: `mov ax, bx`
> Significado: copia o conteúdo do registrador BX para o registrador AX.
> Opcode (código de máquina): **89D8h**
>
> Este é o valor que o processador recebe e executa. O assembler faz exatamente essa substituição mnemônico → número.

## Ver também
- [[ISA]]
- [[Linguagem de Máquina]]
- [[Níveis de Abstração]]
- [[Tradução e Interpretação]]
- [[Registrador]]
- [[Processador]]
