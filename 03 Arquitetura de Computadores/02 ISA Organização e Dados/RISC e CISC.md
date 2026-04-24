---
title: RISC e CISC
aliases:
  - RISC
  - CISC
  - Reduced Instruction Set Computer
  - Complex Instruction Set Computer
  - arquitetura RISC
  - arquitetura CISC
tags:
  - computação/arquitetura
date: 2026-04-06
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# RISC e CISC

## Definição
Duas filosofias opostas de design de [[ISA]]:
- **CISC** (Complex Instruction Set Computer): conjunto de instruções grande e complexo, com muitas instruções especializadas
- **RISC** (Reduced Instruction Set Computer): conjunto de instruções pequeno e simples, com execução direta por hardware

## Relações (SPO)
- CISC → surgiu de → explosão de instruções via [[Microprogramação]] (décadas de 1960–70)
- RISC → surgiu como → reação ao inchaço do CISC (~1985)
- RISC → elimina → [[Microprogramação]] em favor de execução direta por hardware
- RISC → resulta em → [[Processador|processadores]] mais rápidos para cargas de trabalho comuns
- CISC → exemplos → x86 (Intel/AMD), IBM 360
- RISC → exemplos → MIPS (1985), SPARC (1987), DEC Alpha (1992), [[ARM]], [[AVR]]

## Contexto histórico

Durante as décadas de 1960–70, a [[Microprogramação]] tornou trivial adicionar novas instruções — e os projetistas o fizeram abundantemente ([[Microprogramação#Migração de funcionalidade para microcódigo]]). O resultado foram arquiteturas CISC com centenas de instruções raramente usadas.

Pesquisadores perceberam que a maioria dos programas usava apenas um pequeno subconjunto dessas instruções e que as instruções complexas raramente eram geradas pelos compiladores. A solução: **eliminar o microprograma**, reduzir o conjunto de instruções e executar tudo diretamente em hardware — máquinas mais simples, mas mais rápidas.

## Comparação

| | CISC | RISC |
|---|---|---|
| Tamanho do ISA | Grande (centenas de instruções) | Pequeno (dezenas de instruções) |
| **Tamanho das instruções** | **Variável** | **Padronizado (fixo)** |
| Implementação | Via [[Microprogramação|microcódigo]] | Hardware direto |
| Instruções complexas | Sim (multiplicação, strings, etc.) | Não — compostas de instruções simples |
| Microcódigo / microinstruções | Sim — cada instrução quebrada em etapas internas | Não |
| Registradores | Poucos | Muitos |
| Tempo de execução por instrução | **Variável** | **Uniforme** |
| Complexidade do compilador | Menor | Maior |
| Exemplos históricos | IBM 360, x86, VAX | MIPS, SPARC, Alpha, ARM |
| Desempenho geral | Menor (complexidade interna) | Maior |

### Microcódigo e Microinstruções (CISC)
Em CISC, cada instrução da ISA é convertida em uma lista de **microinstruções** armazenadas no **microcódigo** — instruções que existem apenas internamente no processador. O processador executa todas as microinstruções antes de concluir a instrução ISA. Isso torna a execução de cada instrução **mais lenta e de duração variável**.

### Tradeoff compilador vs. processador

| Aspecto | CISC | RISC |
|---|---|---|
| Trabalho do compilador | Simples — mapeia comandos em poucas instruções | Complexo — decompõe em muitas instruções simples |
| Trabalho do processador | Pesado — quebra cada instrução em microinstruções | Leve — executa diretamente |
| Instruções por tarefa | Poucas | Muitas |
| Velocidade de execução | Lenta por instrução | Rápida por instrução |
| Resultado | RISC é mais rápido no total |

> [!example] Exemplo hipotético — divisão
> - **CISC:** compilador emite 1 instrução `DIV`. O processador a quebra em dezenas de microinstruções internamente.
> - **RISC (sem instrução de divisão):** compilador emite 10–20 instruções simples que ao final produzem o quociente. Cada uma executa em 1 ciclo.
>
> Mesmo executando 20× mais instruções, o RISC pode terminar mais rápido porque não há overhead de microcódigo.

### Tempo de Execução e Ciclos de Máquina

Em arquiteturas CISC (como x86), o tempo de execução de cada instrução é medido em **ciclos de máquina** (pulsos do clock interno do processador) e **não é fixo** — varia conforme a complexidade da instrução.

Além disso, uma mesma instrução pode ter tempos diferentes em modelos distintos do processador: fabricantes podem otimizar instruções específicas em novos modelos, reduzindo os ciclos necessários.

> [!warning] Clock interno ≠ desempenho
> Um processador com clock menor pode ser mais rápido que outro de clock maior se executar suas instruções em menos ciclos. A medida relevante de desempenho não é o clock bruto, mas a quantidade de ciclos necessários para cada instrução.

### Comprimento Variável das Instruções (CISC)

Em CISC, o comprimento total (em bits) de uma instrução varia conforme o que ela faz. Exemplo em x86:

| Instrução | Código gerado | Comprimento |
|---|---|---|
| `mov ax, bx` | `89D8h` | 16 bits |
| `mov ax, 1234h` | `B8 1234h` | 24 bits |

O **dado imediato** requerido pela instrução (ex: `1234h`) é considerado parte da instrução pelo processador. O **opcode** da instrução pode ser um número de 8 bits, enquanto os operandos imediatos adicionam bytes variáveis.

Em RISC, todas as instruções têm comprimento padronizado (fixo), simplificando o hardware de decodificação.

### Motivação: Lei de Pareto (80/20)
A tecnologia RISC foi motivada pela **Lei de Pareto** (também chamada Lei 80/20 ou Lei do Menor Esforço):

> "80% do trabalho é conseguido com apenas 20% do esforço."

Pesquisadores descobriram que processadores CISC executam apenas **20% das instruções disponíveis em 80% do tempo**. As instruções complexas raramente eram geradas pelos compiladores. Conclusão: um processador com 20% da complexidade pode obter 80% do resultado — e, por ser mais simples internamente, executar cada instrução muito mais rápido.

> [!note] Estado atual
> O limite entre RISC e CISC tornou-se difuso. Processadores x86 modernos são CISC externamente (ISA) mas traduzem internamente para micro-operações RISC antes de executar. A [[Microprogramação]] voltou, de forma diferente.

## Superescalar e ILP
Evolução posterior ao RISC: CPUs que executam **múltiplas instruções simultaneamente**, às vezes fora de ordem em relação ao programa original. Surgiu nos anos 1990 (IBM RS6000, 1990 — primeira máquina superescalar).

Processadores superescalares sustentam taxas **superiores a 1 instrução por ciclo** de clock, explorando o **paralelismo a nível de instrução (ILP)**. Programadores que conhecem o modelo superescalar podem estruturar código de forma a gerar maior ILP e, portanto, maior desempenho.

A técnica fundamental de ILP é o **pipeline**: as etapas de execução de uma instrução (fetch, decode, execute, write-back) são distribuídas em estágios que operam em paralelo sobre instruções diferentes. Um design simples de pipeline pode sustentar taxa próxima de 1 instrução por ciclo.

Ver [[Concorrência e Paralelismo#Paralelismo a nível de instrução]].

## Ver também
- [[ISA]]
- [[Microprogramação]]
- [[Níveis de Abstração]]
- [[Processador]]
- [[História da Computação]]
- [[Concorrência e Paralelismo]] — ILP, pipeline, superescalar em detalhe
