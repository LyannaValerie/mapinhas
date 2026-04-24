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

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

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

## Modelo de 6 Estágios (SEQ — processador sequencial)

Organização canônica de pipeline com 6 estágios usada em projetos didáticos (ex: [[Y86-64]]):

| Estágio | Sigla | Função |
|---|---|---|
| **Fetch** | F | Lê instrução da memória; calcula valP (PC+tamanho) |
| **Decode** | D | Lê registradores fonte (valA, valB) |
| **Execute** | E | ALU calcula resultado (valE) ou endereço efetivo |
| **Memory** | M | Lê ou escreve dado na memória (valM) |
| **Write-back** | W | Escreve resultados no banco de registradores |
| **PC update** | — | Calcula próximo PC (valP, valC ou valM) |

No modelo **SEQ**, todos os estágios ocorrem num único ciclo de clock. No modelo **PIPE**, cada estágio tem seu registrador de pipeline e opera em paralelo sobre instruções distintas.

## Forwarding (Data Bypassing)

Técnica que elimina stalls RAW passando o resultado de um estágio posterior diretamente como entrada de um estágio anterior, sem esperar o write-back.

### 5 fontes de forwarding (em pipeline de 5 estágios)

| Sinal | Origem | Descrição |
|---|---|---|
| `e_valE` | Execute | saída da ALU (instrução atual em Execute) |
| `m_valM` | Memory | dado lido da memória (instrução atual em Memory) |
| `M_valE` | reg. pipeline M | valE pendente para write-back via porta E |
| `W_valM` | reg. pipeline W | valM pendente para write-back via porta M |
| `W_valE` | reg. pipeline W | valE pendente para write-back via porta E |

### 2 destinos de forwarding
- `valA` — operando fonte A do estágio Decode
- `valB` — operando fonte B do estágio Decode

### Prioridade
Quando múltiplas fontes cobrem o mesmo registrador: **fonte no estágio mais cedo tem prioridade** (instrução mais recente no programa). Exemplo: se `e_dstE` e `M_dstE` apontam para o mesmo registrador, usar `e_valE`.

## Hazard de Load/Use

Caso que **não pode ser resolvido só por forwarding**: instrução de carga (`mrmovq`, `popq`) seguida imediatamente por instrução que usa o registrador carregado.

O dado só fica disponível ao sair do estágio Memory — mas a próxima instrução já precisa dele no estágio Execute do mesmo ciclo. Solução: **stall de 1 ciclo + forwarding**.

Condição de detecção:
```
E_icode ∈ {mrmovq, popq}  AND  E_dstM ∈ {d_srcA, d_srcB}
```

Efeito no ciclo seguinte: bolha injetada em Execute; Decode e Fetch travados (stall).

## Controle de Pipeline — Stall vs Bubble

| Mecanismo | Efeito no registrador de pipeline |
|---|---|
| **Normal** | carrega novo valor na borda de subida do clock |
| **Stall** | mantém valor atual (instrução não avança) |
| **Bubble** | força estado de nop (instrução cancelada) |

### Condições que exigem controle especial

| Condição | Ação |
|---|---|
| Load/use hazard | Stall em F e D; bubble em E |
| Instrução `ret` em D/E/M | Stall em F; bubble em D (por 3 ciclos) |
| Branch mispredicted | Bubble em D e E; fetch do caminho correto |
| Exceção em M ou W | Bubble em M; stall em W |

## CPI — Cycles Per Instruction

Métrica de eficiência do pipeline. CPI ideal = 1,0.

```
CPI = 1,0 + lp + mp + rp
```

| Penalidade | Nome | Cálculo típico |
|---|---|---|
| **lp** | load/use | freq_load × freq_hazard × 1 bolha |
| **mp** | branch misprediction | freq_branch × freq_miss × 2 bolhas |
| **rp** | return | freq_ret × 1,0 × 3 bolhas |

### Valores de referência (benchmark típico)

| Parâmetro | Valor |
|---|---|
| Instruções de carga | 25% das instruções |
| Load/use hazard (dado uso imediato) | 20% das cargas |
| Desvios condicionais | 20% das instruções |
| Misprediction (always-taken) | 40% dos desvios |
| Instruções ret | 2% das instruções |

```
lp = 0,25 × 0,20 × 1 = 0,05
mp = 0,20 × 0,40 × 2 = 0,16
rp = 0,02 × 1,00 × 3 = 0,06
CPI ≈ 1,27
```

> [!note] Maior fonte de penalidade
> Misprediction de branches (mp=0,16) domina. Reduzir erros de [[Previsão de Desvio]] de 40% → 35% baixa o CPI de 1,27 para 1,25.

## Relação com desempenho

O número de estágios (profundidade do pipeline) afeta o desempenho:
- Mais estágios → clock mais alto possível (cada etapa é mais simples)
- Mais estágios → penalidade maior por stall (mais instruções a descartar)
- Overhead dos registradores de pipeline limita ganho: `throughput = 1000 / (delay_lógica/k + 20ps)`

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
- [[Y86-64]] — ISA pedagógica com implementações SEQ e PIPE detalhadas
