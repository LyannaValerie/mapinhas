---
title: PC
aliases:
  - Personal Computer
  - computador pessoal
  - padrão PC
  - linha PC
  - IBM PC
tags:
  - computação/fundamentos
  - computação/histórico
date: 2026-04-06
---

# PC — Personal Computer

## Definição
Padrão de computador pessoal originalmente criado pela IBM em **1981**. Define uma arquitetura com [[Arquitetura Aberta e Fechada|arquitetura aberta]], garantindo compatibilidade de [[Hardware]] e [[Software]] entre máquinas de diferentes fabricantes.

## Relações (SPO)
- PC → criado por → IBM (1981)
- PC → segue → [[Arquitetura Aberta e Fechada|arquitetura aberta]]
- PC → baseado em → [[Modelo de Von Neumann]]
- PC → sobreviveu junto com → Mac (Apple) — únicos padrões remanescentes
- PC → detém → ~90% do mercado de computadores pessoais

## Contexto Histórico
Na década de 1970, havia dezenas de padrões de computadores pessoais incompatíveis entre si — em hardware e software. Apenas dois sobreviveram:

| Linha | Criadora | Mercado |
|---|---|---|
| **PC** | IBM (1981) | ~90% |
| **Mac** (Macintosh) | Apple | ~10% |

Os fatores de sobrevivência do PC foram:
1. **Compatibilidade retroativa** — computadores mais novos rodavam programas mais antigos
2. **[[Arquitetura Aberta e Fechada|Arquitetura aberta]]** — outros fabricantes podiam desenvolver hardware e software
3. **Explosão de clones** — concorrência e maior variedade de computadores compatíveis

> [!note] Modelos históricos IBM
> PC (1981) → PC XT (1983) → PC AT (1984) → PS/2 (1987). Desde 2004, a IBM não fabrica mais computadores pessoais.

## Mapeamento Von Neumann → PC

| Componente do PC                          | Módulo Von Neumann            |
| ----------------------------------------- | ----------------------------- |
| [[Processador]] (CA + CC)                 | CA + CC                       |
| [[Chipset]]                               | CC (parcialmente)             |
| [[Memória]] (RAM)                         | M                             |
| Slots de expansão / Portas de comunicação | Caminhos de E/S               |
| [[Placa-mãe]]                             | Suporte físico / interconexão |
| Placa de vídeo                            | R                             |
| [[Memória de Massa]] (HD, SSD etc.)       | R                             |
| Monitor de vídeo                          | R (saída)                     |
| Teclado, mouse etc.                       | R (entrada)                   |
| Impressora, caixas de som etc.            | R (saída)                     |

## Componentes Internos Típicos

- **[[Processador]]** — "cérebro"; CA + CC de Von Neumann
- **Cooler do processador** — refrigeração; necessário pois o processador gera muito calor
- **[[Memória]] (RAM)** — de onde o processador busca instruções e dados
- **[[Placa-mãe]]** — suporte físico e conexão elétrica entre todos os componentes
- **[[Chipset]]** — conjunto de chips que permite comunicação do processador com outros dispositivos
- **Slots de expansão** — conectores para placas de expansão (PCI Express etc.)
- **Placa de vídeo** — gera o sinal de vídeo; pode estar embutida no processador
- **[[Memória de Massa]]** — HD, SSD, unidades ópticas; armazenamento persistente
- **Portas de comunicação** — SATA, USB, rede, áudio etc.
- **Fonte de alimentação** — converte tensão da rede elétrica nas tensões requeridas pelos componentes

## Componentes Externos Típicos

- Monitor de vídeo — principal dispositivo de saída
- Gabinete — caixa que protege e organiza os componentes internos
- Teclado e mouse — dispositivos de entrada padrão
- Dispositivos de áudio — caixas de som, fones, headsets

## Variantes de formato

| Formato | Nota |
|---|---|
| Desktop (mesa) | Componentes modulares e substituíveis |
| [[Notebook]] (portátil) | Componentes soldados, menor consumo; mesma arquitetura |

## Ver também
- [[Modelo de Von Neumann]]
- [[Arquitetura Aberta e Fechada]]
- [[Processador]]
- [[Memória]]
- [[Placa-mãe]]
- [[Chipset]]
- [[Memória de Massa]]
- [[Notebook]]
- [[Hardware]]
