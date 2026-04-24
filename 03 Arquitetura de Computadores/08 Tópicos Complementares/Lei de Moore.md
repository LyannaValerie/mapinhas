---
tags:
  - arquitetura/história
  - arquitetura/tecnologia
  - hardware/semicondutor
aliases:
  - Moore's Law
  - Dennard scaling
  - escalonamento de Dennard
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Lei de Moore

Observação empírica de Gordon Moore (1965): o número de transistores por chip dobra aproximadamente a cada dois anos.

- Versão original (1965): dobra a cada 1 ano
- Revisão (1975): dobra a cada 2 anos
- Durou ~50 anos como predição válida

## Impacto no Desempenho

| Período | Taxa de melhoria | Dobra a cada |
|---|---|---|
| Antes de 1986 | ~22%/ano | ~3,5 anos |
| 1986–2003 | ~52%/ano | ~2 anos |
| 2003–2011 | ~23%/ano | ~3,5 anos |
| 2011–2015 | ~12%/ano | ~8 anos |
| 2015+ | ~3,5%/ano | ~20 anos |

> [!note] A aceleração de 1986–2003
> Atribuída a inovações arquiteturais (RISC, ILP, caches), não só à tecnologia de semicondutores.

## Fim da Lei de Moore

O número de dispositivos ainda cresce, mas em ritmo decrescente. O tempo de dobramento se estira a cada geração.

- **DRAM**: crescimento desacelerou drasticamente. DRAM de 8 Gb (2014), 16 Gb chegou apenas em 2019. DRAM de 32 Gb pode não existir.
- **Flash/NAND**: crescimento continua, mas via empilhamento 3D, não apenas redução de feature size.

## Escalonamento de Dennard

Robert Dennard (1974): ao reduzir dimensões do transistor por fator $k$, corrente e voltagem também reduzem por $k$. Logo: potência cai por $k^2$, chips mais rápidos e mais frios.

$$P = C \cdot V^2 \cdot f \quad \xrightarrow{\text{Dennard}} \quad P_{\text{constante}} \text{ com } f \uparrow$$

**Fim do escalonamento de Dennard** (~2004): ao atingir feature sizes menores, a tensão de threshold (limiar) não pode mais ser reduzida — a corrente de fuga (leakage) estática se tornaria dominante.

$$P_{\text{static}} = I_{\text{static}} \times V$$

Com potência estática significativa, reduzir voltagem aumenta proporcionalmente o custo estático, eliminando o ganho.

## Consequências do Fim de Ambas as Leis

1. **Fim do IPC gratuito**: não é possível explorar mais ILP sem custo energético proibitivo ("power wall" e "ILP wall")
2. **Virada para multicore** (~2005): mais núcleos no chip em vez de núcleo único mais rápido
3. **Paralelismo explícito**: DLP, TLP e RLP passam a ser responsabilidade do programador
4. **Especialização**: futuro dos processadores são núcleos domain-specific (aceleradores para IA, DSP, etc.)

> [!warning] Falácia
> "Múltiplos processadores são bala de prata."
>
> A transição para multicore não surgiu de avanço que simplificou programação paralela. Foi a única saída disponível. Múltiplos cores não garantem menor consumo — é possível projetar multicore com mais potência total.

## DRAM e a Lacuna de Memória

- DRAM: velocidade cresce ~7%/ano
- Processadores: velocidade crescia ~55%/ano (pré-2003)
- Resultado: largura de banda de memória cresce mais lentamente que compute — "memory wall"

## Caminho Futuro

- **Especialização de domínio**: Chapter 7 de P&H — domain-specific architectures
- Compiladores e linguagens devem expor paralelismo
- "Era La-Z-Boy" do programador (hardware acelera código sequencial automaticamente) encerrada

## Relações

- [[RISC e CISC]] — RISC foi a inovação arquitetural que acelerou ganhos 1986–2003
- [[Concorrência e Paralelismo]] — resposta ao fim das leis de Moore e Dennard
- [[Gerenciamento de Consumo Elétrico]] — consequência direta do fim do escalonamento de Dennard
- [[VLIW]] — tentativa de explorar ILP via compilador (relacionado a IA-64/EPIC)
- [[Custo de Fabricação de Chips]] — tecnologia de processo que impulsionou Moore's Law
