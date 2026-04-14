---
title: ARM
aliases:
  - Advanced RISC Machine
  - Acorn RISC Machine
  - arquitetura ARM
  - ARM ISA
tags:
  - computação/arquitetura
  - computação/histórico
date: 2026-04-06
---

# ARM

## Definição
[[ISA]] [[RISC e CISC|RISC]] de 32/64 bits criada originalmente pela Acorn Computer em 1985. Domina o mercado de dispositivos móveis, embutidos e de baixa potência. Ver [[ISA#As três famílias de ISA mais relevantes]].

## Relações (SPO)
- ARM → originou em → Acorn Computer (1985)
- ARM → inspirada por → projeto RISC de Berkeley (que gerou o [[RISC e CISC|SPARC]])
- ARM → domina → mercado de smartphones, tablets, PDAs e sistemas embutidos
- ARM → licencia projetos → não fabrica chips (modelo de negócio distinto)
- ARM → base para → Samsung Galaxy Tab, Apple Newton, Gameboy Advance, roteadores

## Origem histórica

### Acorn e o BBC Micro
A empresa britânica **Acorn Computer** lançou o **BBC Micro** — computador pessoal de 8 bits baseado no processador **6502**. Com o sucesso, tentou projetar uma segunda máquina para competir com o [[Intel x86|IBM PC]] (baseado no 8086 de 16 bits). Percebeu que o 6502 não tinha força suficiente e que as opções do mercado eram limitadas.

### Inspiração: projeto RISC de Berkeley
Inspirados pelo projeto RISC de Berkeley — onde uma pequena equipe projetou um processador incrivelmente rápido que culminou na arquitetura SPARC — **Steve Furber** e colegas da Acorn decidiram criar sua própria CPU.

Nomearam o projeto **Acorn RISC Machine (ARM)** — depois rebatizado **Advanced RISC Machine** quando o ARM se separou da Acorn.

## Evolução dos projetos

### ARM2 — Acorn Archimedes (1985)
- **Instruções e dados de 32 bits**; espaço de endereços de 26 bits
- Fabricado pela VLSI Technology
- Apareceu no computador **Acorn Archimedes**: até **2 MIPS** de desempenho por £899
- Popular na Grã-Bretanha, Irlanda, Austrália e Nova Zelândia, especialmente em escolas

### ARM 610 — Apple Newton (1993)
- A **Apple** contactou a Acorn para desenvolver um ARM para o **Apple Newton** (primeiro computador palmtop)
- A equipe de arquitetura saiu da Acorn e fundou a empresa independente **Advanced RISC Machines (ARM)**
- O **ARM 610** controlou o Newton quando lançado em 1993
- Novidade: [[Cache]] de **4 KB** — melhora significativa de desempenho vs. ARM original
- Newton não foi grande sucesso comercial, mas o ARM 610 encontrou outras aplicações (ex: Acorn RISC PC)

### StrongARM — parceria com DEC (meados dos anos 1990)
- ARM colaborou com a **Digital Equipment Corporation** para criar versão de alta velocidade e **ultrabaixa potência** para PDAs
- **StrongARM:** 233 MHz, **1 watt** de consumo — causou impacto no setor
- Eficiência por design simples e limpo: duas [[Cache|caches]] de **16 KB** (instruções + dados)
- Aplicações: PDAs, transdutores de TV, dispositivos de mídia, roteadores

### ARM7 (lançado 1994, ainda em uso)
- Caches separadas para instrução e dados
- Incorpora o conjunto de instruções **Thumb** — versão de **16 bits** do ISA completo de 32 bits
  - Permite codificar operações comuns em instruções menores → reduz **significativamente** a memória de programa necessária
- Aplicações: torradeiras, controle de motor, **Nintendo Gameboy Advance**
- O ARM7 é o núcleo mais difundido na família — ainda usado como núcleo de gerenciamento em SoCs modernos

## Modelo de negócio — licenciamento
A ARM **não fabrica** microprocessadores. Cria:
- Projetos de CPU
- Ferramentas e bibliotecas para desenvolvedores

Esses projetos são **licenciados** para projetistas de sistemas e fabricantes de chips. Exemplo do pipeline de produção do Samsung Galaxy Tab:

| Etapa | Empresa |
|---|---|
| Projeto dos núcleos ARM | ARM Holdings |
| Integração no SoC Tegra 2 | Nvidia |
| Fabricação | TSMC (Taiwan) |

> [!info] Colaboração internacional
> Um único chip é projetado por empresas em países diferentes, cada uma agregando valor ao produto final.

## Nvidia Tegra 2 — estudo de caso
SoC presente no Samsung Galaxy Tab (baseado em Android). Conteúdo:

| Componente | Detalhes |
|---|---|
| CPU principal | 2× ARM Cortex-A9 (1,2 GHz) — fora de ordem, emissão dual, [[Cache]] L2 de 1 MB |
| CPU auxiliar | 1× ARM7 — gerenciamento de energia e configuração do sistema |
| GPU | GeForce 333 MHz (baixa potência) |
| Outros | Codificador/decodificador de vídeo; processador de áudio; saída HDMI |

Os Cortex-A9 suportam multiprocessamento com memória compartilhada.

## Escala e expansão
- **Janeiro de 2011:** ARM anunciou **15 bilhões** de processadores vendidos desde o lançamento, com crescimento contínuo
- **Outubro de 2011:** anunciado ARM de **64 bits**
- **Projeto Denver (Nvidia, 2011):** SoC ARM para **servidores e data centers** — múltiplos ARM 64 bits + GPGPU de uso geral; aspectos de baixa potência reduzem custos de resfriamento

> [!note] Alcance do ARM
> Embora nascido para mercados de baixo custo/potência, o ARM demonstra capacidade para qualquer mercado — incluindo servidores de alto desempenho.

## ISA ARM v7 — OMAP4430

### Modelo de Memória
- Espaço de endereçamento linear de **2³² bytes** (uni-espaço)
- **Bi-endian:** configurado por bloco de memória lido na inicialização; o bloco de configuração deve estar em little-endian
- Alinhamento **obrigatório** — acesso desalinhado é inválido (diferente do x86)

> [!warning] Limite de 32 bits
> Smartphones modernos já ultrapassam 4 GB de RAM. A limitação do espaço de 32 bits foi resolvida com a ISA ARM v8 (64 bits). Antes disso, memória extra era acessada como armazenamento em bloco (flash), não como RAM diretamente endereçável.

### Registradores — R0 a R15

| Registrador | Nome alternativo | Função |
|---|---|---|
| R0–R3 | A1–A4 | Passagem de parâmetros para o procedimento chamado |
| R4–R11 | V1–V8 | Variáveis locais do procedimento atual |
| R12 | IP | Registrador de chamada intra-procedimento (desvios > 32 MB) |
| R13 | SP | Ponteiro de pilha |
| R14 | LR | Link Register — endereço de retorno da função atual |
| R15 | PC | Contador de programa |

- 16 registradores de uso geral de **32 bits**
- PC mapeado no arquivo de registradores → desvios possíveis via operações da ULA com destino R15
- PSR (registrador de estado): mantém Zero, Negativo, Overflow e outros flags

**Registradores de ponto flutuante (com coprocessador VFP):**
- 32 registradores de 32 bits acessíveis como:
  - 32 valores de precisão simples, **ou**
  - 16 valores de precisão dupla (64 bits)

### Arquitetura Carga/Armazenamento
Somente `LDR` e `STR` acessam a memória. Todas as operações aritméticas e lógicas operam **exclusivamente** sobre registradores.

```
LDR  R0, [R1]       ; carrega M[R1] em R0
STR  R0, [R1, #8]   ; armazena R0 em M[R1 + 8]
ADD  R2, R0, R3     ; R2 = R0 + R3 (sem acesso à memória)
```

Variantes de carga com especificação de tamanho e sinal:

| Instrução | Operação |
|---|---|
| `LDRSB` | Load byte com extensão de sinal (8 → 32 bits) |
| `LDRB` | Load byte sem sinal (zero-extend) |
| `LDRSH` | Load meia palavra com sinal (16 → 32 bits) |
| `LDRH` | Load meia palavra sem sinal |
| `LDR` | Load palavra (32 bits) |

### Instruções Predicadas
Toda instrução ARM de 32 bits carrega um campo de **condição de 4 bits** (bits 28–31). Se a condição não for satisfeita com base no PSR, o resultado da instrução é descartado.

```
ADDGT R1, R2, R3   ; executa ADD apenas se flag GT (greater than) estiver setado
```

Elimina desvios curtos — melhora o pipeline ao evitar flush por branch not-taken.

### Thumb ISA (16 bits)
Versão reduzida da ISA ARM de 32 bits:
- Mesmas operações, mas instruções de **16 bits**
- Limitação: apenas **8 registradores** (R0–R7) acessíveis
- Destino = uma das fontes (2 operandos em vez de 3)
- Uso: reduzir tamanho do código de programa (importante para sistemas embutidos com Flash limitada)

### Chamadas de Procedimento
- `BLcc IMM`: desvio com link — salva endereço de retorno em **LR (R14)**, desvia para PC + IMM
  - Alcance: ±32 MB (deslocamento de 24 bits × 4)
- `BLcc R1`: desvio com link para endereço em registrador — alcance ilimitado (via IP/R12 como trampolim)
- Retorno: `MOV PC, LR` (copia LR no PC)

## Ver também
- [[ISA]]
- [[RISC e CISC]]
- [[Cache]]
- [[Microcontrolador]]
- [[Intel x86]]
- [[História da Computação]]
- [[Tipos de Computadores]]
- [[Modos de Endereçamento]]
- [[Formatos de Instrução]]
- [[Tipos de Dados ISA]]
- [[Pilha]]
