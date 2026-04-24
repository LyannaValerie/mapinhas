---
title: Arquitetura Vetorial
tags:
  - hardware
  - arquitetura
  - DLP
  - vetor
aliases:
  - vector architecture
  - processador vetorial
  - RV64V
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Arquitetura Vetorial

Tipo de [[Concorrência e Paralelismo#SIMD — Instrução única, múltiplos dados|SIMD]] que estende execução pipelined para múltiplos dados. Instrução vetorial opera sobre **registrador vetorial** — vetor de N elementos — com uma única instrução. Precede SIMD multimídia em ~30 anos; exemplo clássico: **Cray-1 (1976)**, ISA de referência: **RV64V** (extensão vetorial do RISC-V).

## Conceitos fundamentais

| Conceito | Definição |
|---|---|
| **Registrador vetorial** | Conjunto de N elementos (ex.: 64 doubles em RV64V) carregado da memória de uma vez |
| **Unidades funcionais vetoriais** | Pipelined: VFAdd, VFMul, VFDiv, VLoad, VStore — cada uma aceita operandos vetoriais |
| **Registrador de comprimento vetorial (vl)** | Indica quantos elementos a instrução atual processa; ≤ MVL |
| **MVL (Maximum Vector Length)** | Comprimento máximo suportado pelo hardware |
| **Taxa de iniciação** | Velocidade com que a unidade consome novos operandos/produz resultados (RV64V: 1 elemento/ciclo por lane) |

## Modelo de execução

### Convoy e Chime

- **Convoy**: conjunto de instruções vetoriais sem hazards estruturais entre si — podem executar juntas
- **Chime**: tempo para executar 1 convoy ≈ comprimento do vetor (ciclos)
- Estimativa de desempenho: `T ≈ n_convoys × chime × comprimento_vetor`

```
Exemplo DAXPY vetorial:
  Convoy 1: vld (X)  +  vmul (a×X)      ← chaining permite dependência RAW
  Convoy 2: vld (Y)  +  vadd (a×X + Y)  ← novo convoy: hazard estrutural em VLoad
  Convoy 3: vst (resultado)
  → 3 chimes para vetores de comprimento 64 = ~96 ciclos
```

### Chaining

Equivalente ao forwarding escalar: permite iniciar instrução dependente assim que **elementos individuais** do operando ficam prontos — sem esperar o vetor inteiro.

- **Flexible chaining**: qualquer instrução vetorial pode encadear com qualquer outra ativa (sem hazard estrutural)
- Implementação: leitura e escrita simultâneas no mesmo registrador vetorial (elementos diferentes)

### Start-up time (RV64V / Cray-1)

| Unidade | Latência (ciclos) |
|---|---|
| FP add | 6 |
| FP multiply | 7 |
| FP divide | 20 |
| Vector load / store | 12 |

O tempo real de execução inclui o start-up time; o modelo de chimes o ignora (é uma aproximação). Ver [[Desempenho de Processadores Vetoriais]] para o modelo completo com $T_n$, $R_\infty$, $N_{1/2}$ e $N_v$.

## Múltiplas Lanes

Aumenta throughput sem alterar a ISA vetorial:

- Cada **lane** contém: uma fatia do banco de registradores vetoriais + uma pipeline de cada unidade funcional
- N lanes → N elementos processados por ciclo → tempo de execução ÷ N
- Elemento `i` sempre vai para a lane `i mod N`

```
vld + vmul + vadd com 4 lanes:
  todos os 4 elementos de um chime executam em paralelo
  → 4× throughput sobre 1 lane
```

> [!warning] Dead time com muitas lanes
> Algumas implementações inserem ciclos de **dead time** entre instruções ao mesmo functional unit.
> Com mais lanes, o vetor por lane é mais curto → dead time representa fração maior → pipelined start-up torna-se crítico.
> Exemplo: Cray C90 (2 lanes, dead time=4): eficiência=94%. Com 16 lanes: eficiência=67%.

## Strip Mining

Vetor de tamanho arbitrário `n > MVL`: dividir em blocos de tamanho MVL.

```asm
vl = min(MVL, n)        ; primeiro bloco: n % MVL (resto inicial)
loop:
  vld X, vl             ; carrega vl elementos
  <operações>
  n -= vl
  vl = MVL              ; blocos seguintes: tamanho máximo
  if n != 0: goto loop
```

Garante que cada iteração usa o vetor inteiro exceto a primeira, que cobre o "resto" (`n % MVL`).

## Stride (acesso não-unitário)

Para dados não contíguos (ex.: colunas de matriz em row-major):

```asm
vld.stride X, base, stride   ; carrega elementos separados por 'stride' bytes
```

- Stride = 1: acesso sequencial (unit-stride) — mais eficiente
- Stride > 1: bancos de memória podem conflitar se `stride % n_banks == 0`
- Necessário para operações com matrizes em ordens transpostas

## Scatter / Gather (acesso indexado)

Para matrizes esparsas e índices indiretos:

```asm
vld.indexed A, base, K[]   ; gather: A[i] = mem[base + K[i]]
vst.indexed A, base, K[]   ; scatter: mem[base + K[i]] = A[i]
```

- Cada elemento tem endereço individual → sem agrupamento → lento (conflitos de banco em todo o sistema)
- Compilador precisa de **diretiva do programador** para saber que K[i] são distintos (sem dependências)
- GPUs generalizam: **todo load é gather, todo store é scatter**

## Registradores de Máscara (Predicate Registers)

Permitem execução condicional dentro do vetor (IF-conversion):

```c
for (i = 0; i < 64; i++)
    if (X[i] != 0) X[i] -= Y[i];
```

```asm
setp p0, X, 0.0          ; p0[i] = (X[i] != 0)
vsub.masked X, X, Y, p0  ; subtrai apenas onde p0[i] = 1
```

- Elementos com máscara=0 ainda **executam** mas resultado é descartado → mesmo tempo de execução
- IF-conversion elimina branch e dependência de controle → pode ser mais rápido mesmo com "trabalho inútil"
- Em contraste com GPUs: aqui os registradores de predicado são **arquiteturais** (visíveis ao compilador)

## Bancos de Memória

Para atingir 1 elemento/ciclo, o sistema de memória precisa de múltiplos bancos independentes:

- N bancos → N acessos simultâneos (se sem conflito)
- Conflito de banco: ocorre quando stride é múltiplo do número de bancos
  - Ex: 16 bancos, stride=16 → todos acessam o mesmo banco → serialização

## Relações (SPO)

- Arquitetura vetorial → implementa → [[Concorrência e Paralelismo#SIMD — Instrução única, múltiplos dados|DLP]] via pipelining profundo
- Convoy → agrupa → instruções sem hazard estrutural
- Chaining → equivale a → forwarding em pipeline escalar
- Strip mining → generaliza → vetores de comprimento arbitrário via vl
- Gather/scatter → habilita → acesso a matrizes esparsas
- Multiple lanes → escala → throughput sem mudar ISA
- RV64V → baseia-se em → Cray-1 (latências iguais)

## Limitações vs. SIMD multimídia

| Aspecto | Arquitetura vetorial | SIMD multimídia (SSE/AVX) |
|---|---|---|
| Largura | Dinâmica (vl ≤ MVL) | Fixa (128/256/512 bits) |
| Chaining | Suportado | Não |
| Strip mining | Nativo | Manual / compilador |
| Scatter/gather | Instrução dedicada | Emulado ou limitado |
| Programabilidade | Mais fácil para compiladores vetorizadores | Requer assembly ou intrinsics |

## Ver também

- [[Concorrência e Paralelismo]] — DLP, ILP, TLP; taxonomia de Flynn
- [[Instruções SIMD]] — extensões x86: MMX, SSE, AVX (modelo de largura fixa)
- [[GPU — Arquitetura SIMD]] — generalização: todo load é gather; máscara por hardware
- [[Modelo Roofline]] — ferramenta para analisar gargalo compute vs. memória
- [[Pipeline]] — chaining é análogo ao forwarding escalar
- [[Hierarquia de Memória]] — bancos de memória para bandwidth vetorial
- [[Desempenho de Processadores Vetoriais]] — Tn formula, R∞, N1/2, Nv, DAXPY, tailgating, dead time
- [[Sistema de Memória Vetorial]] — timing de banco, conflitos de stride, DRAM vs. SRAM
- [[Cray X1]] — processador vetorial moderno: MSP/SSP, 2048 requests in-flight, X1E multicore
