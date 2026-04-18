---
title: Microprogramação
aliases:
  - microprograma
  - microcódigo
  - microprogramming
tags:
  - computação/arquitetura
  - computação/histórico
date: 2026-04-06
---

# Microprogramação

## Definição
Técnica de implementar o nível [[ISA]] através de um interpretador embutido e imutável (o **microprograma**), em vez de executá-lo diretamente por circuitos dedicados. Opera no nível 1 — [[Níveis de Abstração|microarquitetura]].

## Relações (SPO)
- Microprogramação → proposta por → Maurice Wilkes (1951, Universidade de Cambridge)
- Microprogramação → simplifica → circuitos do [[Hardware]] (nível lógico digital)
- Microprograma → interpreta → instruções do nível [[ISA]]
- Microprograma → executa sobre → caminho de dados ([[Registrador|registradores]] + [[ULA]])
- Microprogramação → tornou-se dominante em → ~1970

## Origem histórica

> [!info] O problema que Wilkes resolveu
> Nos anos 1940, computadores tinham apenas 2 níveis (ISA + lógico digital). Os circuitos para executar diretamente o nível ISA eram **complexos, difíceis de montar e não confiáveis** — pois dependiam de válvulas eletrônicas, que falhavam com frequência.
>
> Em **1951**, Maurice Wilkes propôs inserir um nível intermediário: um microprograma imutável que interpretasse as instruções ISA. Com isso, o hardware só precisaria executar microprogramas — com conjunto de instruções muito menor e mais simples — reduzindo drasticamente o número de válvulas e aumentando a confiabilidade.

### Linha do tempo
| Período | Evento |
|---|---|
| 1940s | Computadores com apenas 2 níveis (ISA + lógico digital); circuitos complexos e frágeis |
| 1951 | Wilkes propõe a microprogramação (máquina de 3 níveis) |
| 1950s–1960s | Primeiras máquinas microprogramadas construídas gradualmente |
| ~1970 | Microprogramação torna-se dominante; todas as principais máquinas a utilizam |

## Migração de funcionalidade para microcódigo

Após ~1970, projetistas perceberam que podiam adicionar novas instruções simplesmente **ampliando o microprograma** — em vez de alterar circuitos físicos. Isso gerou uma explosão de conjuntos de instruções. Exemplos de instruções adicionadas:

**Aritméticas:**
- Multiplicação e divisão de inteiros
- Aritmética de ponto flutuante
- INC (incremento) — mais rápido que ADD para +1

**Controle de fluxo:**
- Chamada e retorno de procedimentos
- Aceleração de laços (loops)

**Dados:**
- Manipulação de cadeias de caracteres
- Indexação e endereçamento indireto (vetores)

**Sistema:**
- Relocação — mover programas na memória durante execução
- Interrupções — avisar o processador ao concluir E/S
- Comutação de processos (process switching)
- Instruções para áudio, imagem e multimídia

> [!note] Custo da complexidade
> Muitas dessas instruções não eram essencialmente necessárias — seus efeitos podiam ser obtidos com instruções existentes, apenas de forma um pouco mais lenta. A microprogramação tornou fácil demais adicionar instruções, levando a conjuntos de instruções excessivamente grandes (o que mais tarde motivaria o movimento [[ISA|RISC]]).

## Eliminação e o movimento RISC

Com o passar do tempo, os microprogramas tornaram-se cada vez maiores e mais lentos. Pesquisadores perceberam que:
- **Eliminar o microprograma** e executar instruções ISA diretamente por hardware
- **Reduzir drasticamente o conjunto de instruções** (ISA mais simples)

...resultava em máquinas **mais rápidas**. Esse movimento ficou conhecido como **RISC** (Reduced Instruction Set Computer). Em certo sentido, o projeto voltou ao ponto de partida — anterior à invenção de Wilkes.

> [!info] Estado atual
> Processadores modernos fazem as duas coisas: usam microprogramação para traduzir instruções complexas em microcódigo interno simples, executado diretamente no hardware. A fronteira entre hardware e software é **arbitrária e muda constantemente**: o software de hoje pode ser o hardware de amanhã, e vice-versa.

Ver [[História da Computação#Marcos do Computador Digital Moderno]] para exemplos de máquinas RISC (MIPS 1985, SPARC 1987).

## Mic-1: Microarquitetura de Referência (Tanenbaum)

Exemplo concreto de implementação microprogramada, usado como modelo didático.

### Controle Store
- ROM de **512 × 36 bits** contendo todas as microinstruções.
- Endereçado por **MPC** (Micro Program Counter, 9 bits).
- **MIR** (Micro Instruction Register): carrega a microinstrução corrente.

### Formato da Microinstrução (36 bits)
```
[ Addr(9) | JAM(3) | ULA(8) | C(9) | Mem(3) | B(4) ]
```
- **Addr**: endereço da próxima microinstrução.
- **JAM**: JMPC, JAMN, JAMZ — controle de desvio condicional.
- **ULA**: seleciona operação (SLL8, SRA1, funções lógicas/aritméticas).
- **C**: máscara de 9 bits — quais registradores recebem o resultado.
- **Mem**: READ (MDR ← mem[MAR]), WRITE (mem[MAR] ← MDR), FETCH (MBR ← mem[PC]; PC++).
- **B**: 4 bits — seleciona registrador fonte para barramento B (0=MDR, 1=PC, …, 8=MBR).

### Despacho JMPC
Quando JMPC=1, os 8 bits baixos do MPC recebem MBR (opcode da instrução corrente). Isso implementa uma **tabela de despacho**: cada opcode salta diretamente para a microrotina correspondente.

### Registradores do Datapath
MAR, MDR, PC, MBR, SP, LV, CPP, TOS, OPC, H. Ver [[Caminho de Dados]] para detalhes.

## IFU — Instruction Fetch Unit (Mic-2)

Unidade que busca instruções de forma antecipada, desacoplando o acesso à memória de programa do ciclo de execução:

- **IMAR**: PC independente da IFU (Instruction Memory Address Register).
- **Registrador de prefetch (shift register)**: 8 bytes; alimenta MBR1 (8 bits) e MBR2 (16 bits).
- **FSM de 7 estados** gerencia o preenchimento contínuo do shift register.
- Resultado: MBR sempre contém o próximo opcode/operando sem stalls por memória de instrução.

## Evolução Mic-1 → Mic-4

| Versão | Inovação principal | CPI médio |
|---|---|---|
| **Mic-1** | Controle store + datapath básico | ~4–5 |
| **Mic-2** | IFU (prefetch de instrução) | ~3–4 |
| **Mic-3** | Pipeline de 3 estágios com latches A/B/C | ~2 |
| **Mic-4** | Pipeline de 7 estágios (IFU/D1/D2/EX/MEM/WB) | ~1 |

### Mic-3: Pipeline de Microinstruções
Insere **latches A, B, C** entre estágios do datapath:
- Latch A: separa leitura de registradores da operação ULA.
- Latch B: separa ULA da escrita de resultados.
- Latch C: separa memória de escrita final.

Hazard RAW: se microinstrução N escreve em R e N+1 lê R, o valor ainda não foi commitado. Solução: **stall** (bolha inserida) ou detecção por hardware com forwarding.

### Mic-4: Pipeline de Estágios IJVM
Sete estágios mapeados ao nível de instrução IJVM (não microinstrução):
```
IFU → D1 (decodifica) → D2 (busca operandos) → EX (executa) → MEM → WB
```
Reduz CPI próximo de 1 para instruções sem hazard.

## MAL — Micro Assembly Language

Linguagem simbólica para escrever microprogramas:
```
MAIN1  MAR = PC = PC + 1; rd
MAIN2  Z = TOS
MAIN3  if (Z) goto T; else goto F
```
- Cada linha = uma microinstrução.
- Atribuições paralelas são permitidas se não conflitantes.
- `rd`/`wr`/`fetch` ativam sinais de memória.

## Ver também
- [[Níveis de Abstração]]
- [[ISA]]
- [[ULA]]
- [[Registrador]]
- [[Hardware]]
- [[História da Computação]]
- [[Caminho de Dados]] — datapath do Mic-1
- [[Pipeline]] — evolução Mic-3/Mic-4
- [[Pilha — IJVM]] — SP/LV como registradores do datapath
