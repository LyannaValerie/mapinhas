---
title: Pipeline
aliases:
  - pipelining
  - pipeline de instruções
  - unidade de busca
  - unidade de decodificação
  - unidade de execução
  - fetch unit
  - decode unit
  - execute unit
  - pré-busca
  - prefetch
tags:
  - computação/arquitetura
date: 2026-04-09
---

# Pipeline

## Definição
Técnica de organização interna do [[Processador]] que divide a execução de instruções em **estágios sequenciais**, permitindo que múltiplas instruções sejam processadas simultaneamente — cada uma em um estágio diferente.

O nome é uma analogia a um pipeline industrial: enquanto a instrução 1 está na execução, a instrução 2 já está na decodificação e a instrução 3 já está sendo buscada da memória.

## Relações (SPO)
- Pipeline → divide execução em → estágios (busca, decodificação, execução)
- Pipeline → permite → paralelismo a nível de instrução ([[Concorrência e Paralelismo#Paralelismo a nível de instrução|ILP]])
- Pipeline → aplicado em → processadores [[RISC e CISC|CISC]] e [[RISC e CISC|RISC]]
- Stall → interrompe → fluxo do pipeline

## Arquitetura básica CISC — 3 estágios

Todo processador CISC possui pelo menos três unidades internas:

```
MEMÓRIA (RAM)
     ↓
UNIDADE DE BUSCA  →  UNIDADE DE DECODIFICAÇÃO  →  UNIDADE DE EXECUÇÃO
```

### Unidade de Busca (Fetch)
Busca o conteúdo do endereço apontado pelo [[Processador#Program Counter (PC) — Contador de Programa|Program Counter (PC)]] — a "posição atual" na memória. O que está na memória são apenas **números binários**; a interpretação do significado fica para a próxima etapa.

Também carrega **operandos adicionais** quando a unidade de decodificação os solicita (ex: o dado imediato de uma instrução como `mov ax, 1234h`).

**Pré-busca (Prefetch):** mecanismo presente desde o Intel 8086. A unidade de busca carrega antecipadamente os conteúdos dos próximos endereços de memória, pois é alta a probabilidade de serem necessários. Atualmente integrado ao sistema de [[Cache]].

### Unidade de Decodificação (Decode)
Recebe o número binário da unidade de busca e **consulta a tabela interna** (o [[ISA|conjunto de instruções]]) para determinar qual instrução ele representa.

Responsabilidades:
- Verificar se todos os bytes da instrução foram carregados (instruções CISC têm comprimento variável)
- Para instruções de desvio incondicional (`JMP`/`CALL`): pode atualizar o PC diretamente, economizando o envio à unidade de execução
- Para desvios condicionais: coordenar com a **unidade de previsão de desvios** para buscar antecipadamente os dois caminhos possíveis

### Unidade de Execução (Execute)
Executa efetivamente a instrução. Em instruções CISC, pode demorar **múltiplos ciclos de máquina**. Processadores modernos possuem **múltiplas unidades de execução em paralelo** para aumentar o throughput.

Após a execução, há um estágio adicional de **write-back**: atualização dos [[Registrador|registradores]] ou da memória com o resultado calculado.

### Unidades de Carga e Armazenamento (Load/Store)
Processadores x86 modernos têm unidades separadas para acessar dados da memória:

| Unidade | Responsável por |
|---|---|
| **Unidade de Busca** | Buscar as próximas instruções e seus operandos imediatos |
| **Unidade de Carga** | Carregar dados de posições de memória (ex: `mov ax, [bx]`) |
| **Unidade de Armazenamento** | Gravar dados em posições de memória |

A separação evita que buscas de dados monopolizem a unidade de busca de instruções, aumentando eficiência.

## Pipelining — execução paralela

Em situação ideal, todas as unidades trabalham simultaneamente em instruções diferentes:

```
Clock:        1           2           3           4      ...
Busca:    instrução 1  instrução 2  instrução 3  instrução 4
Decode:       —        instrução 1  instrução 2  instrução 3
Execute:      —            —        instrução 1  instrução 2
```

Para que o fluxo seja constante, **cada unidade precisa gastar o mesmo tempo** por instrução (idealmente 1 ciclo de clock). Quando isso não é possível, ocorrem **stalls** (pausas).

## Hazards — classificação formal

### Hazards de dados

| Tipo | Sigla | Descrição | Exemplo |
|---|---|---|---|
| Read After Write | **RAW** | instrução lê antes da anterior escrever | `ADD R1,R2,R3` → `MOV R4,R1` |
| Write After Read | **WAR** | instrução escreve antes da anterior ler | `MOV R4,R1` → `ADD R1,...` |
| Write After Write | **WAW** | duas escritas no mesmo registrador | `ADD R1,...` → `SUB R1,...` |

- **RAW**: dependência real — requer stall ou forwarding.
- **WAR e WAW**: dependências falsas de nome — eliminadas por [[Execução Fora de Ordem#Register Renaming|register renaming]].

### Hazards de controle
Desvios condicionais: o destino é desconhecido até a condição resolver. Solução: [[Previsão de Desvio]].

### Hazards estruturais
Duas instruções precisam do mesmo recurso no mesmo ciclo (ex: memória unificada instrução+dado).

## Stalls — interrupções no fluxo

### 1. Instrução lenta
A unidade de execução pode precisar de vários ciclos. As unidades anteriores ficam pausadas aguardando. Solução: **fila de instruções** entre decodificação e execução.

### 2. Dependência de dados (RAW)
Se a instrução seguinte depende do resultado da instrução anterior (ainda em execução), o pipeline insere bolha (**stall/protelação**). Alternativa: **forwarding** — resultado disponibilizado diretamente à próxima instrução sem passar pelo write-back.

### 3. Desvio condicional
Sem saber qual caminho o programa tomará, a unidade de busca não pode avançar. A **unidade de previsão de desvios** busca antecipadamente as duas possibilidades. Se a previsão for errada, o pipeline precisa ser descartado e reiniciado (*pipeline flush*). Ver [[Previsão de Desvio]].

> [!tip] Meta do pipeline
> Manter **todas as unidades internas ocupadas** a maior parte do tempo, minimizando stalls e maximizando o aproveitamento do clock.

## Relação com desempenho

O número de estágios (profundidade do pipeline) afeta o desempenho:
- Mais estágios → clock mais alto possível (cada etapa é mais simples)
- Mais estágios → penalidade maior por stall (mais instruções a descartar)

Processadores modernos têm dezenas de estágios. Ver [[Concorrência e Paralelismo]] para superescalar, execução fora de ordem e outras técnicas.

## Ver também
- [[Processador]]
- [[RISC e CISC]]
- [[Cache]] — integrada ao sistema de pré-busca
- [[Concorrência e Paralelismo]] — superescalar, ILP, execução fora de ordem
- [[ISA]] — conjunto de instruções consultado pela unidade de decodificação
- [[Registrador]] — write-back atualiza registradores
- [[Previsão de Desvio]] — mitiga hazards de controle
- [[Execução Fora de Ordem]] — elimina hazards WAR/WAW por renaming
- [[Microprogramação]] — Mic-3/Mic-4 como exemplos didáticos de pipeline
