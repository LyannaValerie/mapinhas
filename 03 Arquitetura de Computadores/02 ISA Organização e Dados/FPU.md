---
title: FPU
aliases:
  - Floating Point Unit
  - Unidade de Ponto Flutuante
  - x87
  - coprocessador matemático
  - coprocessador
  - 8087
  - 287
  - 387
  - 487SX
  - registradores FPU
  - registradores SIMD
  - MMX
  - SSE
  - AVX
  - AVX-512
  - XMM
  - YMM
  - ZMM
  - MM0
  - MM7
tags:
  - computação/arquitetura
date: 2026-04-09
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

	
# FPU — Unidade de Ponto Flutuante

## Definição
Unidade especializada do [[Processador]] responsável por operações matemáticas de **ponto flutuante** (números reais). Em x86, a FPU é chamada de **x87** — herança dos coprocessadores com sufixo 87.

---

## Relações (SPO)
- FPU → executa → operações de ponto flutuante e inteiros longos
- x87 → herdou → nome dos coprocessadores 8087/287/387
- 486DX → integrou → FPU ao processador principal
- Registradores x87 → formam → pilha de 8 registradores de 80 bits
- SIMD → estende → capacidade da FPU com operações vetoriais
- AVX-512 → expande → YMM de 256 para ZMM de 512 bits

---

## História: Coprocessadores Matemáticos

A FPU não nasceu integrada ao processador. Por décadas foi um chip separado (coprocessador), vendido e instalado opcionalmente:

| Processador | Coprocessador | Modo |
|---|---|---|
| 8086 / 8088 | **8087** | externo (chip separado) |
| 286 | **287** | externo |
| 386 | **387** | externo |
| 486SX | **487SX** | externo (versão sem FPU integrada) |
| **486DX** | — | **integrado ao processador** |
| Pentium e posteriores | — | integrado |

> [!note] Por que "x87"?
> O sufixo **87** vem do primeiro coprocessador, o **8087**. Os sucessores (287, 387, 487SX) mantiveram o sufixo. Mesmo após a integração, o conjunto de instruções de ponto flutuante continuou sendo chamado de **x87**.

---

## Registradores de Dados (R0–R7)

A FPU possui **8 registradores de dados** de **80 bits** cada — precisão dupla estendida (extended double precision).

### Organização em pilha

Os registradores não são acessados diretamente pelo número. A FPU usa uma **pilha** (stack):

| Operação | Efeito |
|---|---|
| **STORE** (armazenar → pilha) | equivale a **PUSH** — empurra valor para o topo |
| **LOAD** (carregar ← pilha) | equivale a **POP** — retira valor do topo |

O ponteiro do topo da pilha é o campo **TOP** no **registrador de status** (3 bits, valores 0–7). Não existe registrador SP separado.

```
ST(0) → topo atual da pilha
ST(1) → um abaixo do topo
...
ST(7) → fundo da pilha
```

> [!note] ST(0)–ST(7) vs. R0–R7
> **R0–R7** são os registradores físicos. **ST(0)–ST(7)** são aliases relativos ao TOP: ST(0) = R[TOP], ST(1) = R[(TOP+1) mod 8], etc.

---

## Registrador de Status (16 bits)

Reflete o estado atual da FPU após cada operação.

| Bits | Campo | Função |
|---|---|---|
| 15 | **B** | Busy — FPU ocupada (legado; espelha ES) |
| 14 | **C3** | Condition code 3 |
| 13–11 | **TOP** | Ponteiro do topo da pilha (0–7) |
| 10 | **C2** | Condition code 2 |
| 9 | **C1** | Condition code 1 |
| 8 | **C0** | Condition code 0 |
| 7 | **ES** | Error Summary — ativado se qualquer exceção não mascarada ocorreu |
| 6 | **SF** | Stack Fault — erro de pilha (overflow/underflow) |
| 5 | **PE** | Precision Exception |
| 4 | **UE** | Underflow Exception |
| 3 | **OE** | Overflow Exception |
| 2 | **ZE** | Zero Divide Exception |
| 1 | **DE** | Denormalized Operand Exception |
| 0 | **IE** | Invalid Operation Exception |

Os bits **C0–C3** funcionam como flags de resultado (equivalentes às flags CF/ZF/PF do inteiro) para instruções de comparação.

---

## Registrador de Controle (16 bits)

Configura o comportamento da FPU. Inicializado em **037Fh** pelo processador.

| Bits | Campo | Valores |
|---|---|---|
| 11–10 | **RC** — Rounding Control | `00` = arredondar para o mais próximo (padrão)<br>`01` = arredondar para baixo (−∞)<br>`10` = arredondar para cima (+∞)<br>`11` = arredondar em direção a zero (truncar) |
| 9–8 | **PC** — Precision Control | `00` = precisão simples (24 bits de mantissa)<br>`10` = precisão dupla (53 bits de mantissa)<br>`11` = precisão dupla estendida (64 bits de mantissa) — padrão |
| 5–0 | Máscaras de exceção | Um bit por exceção (PE/UE/OE/ZE/DE/IE); `1` = mascarada (silenciosa) |

> [!note] 037Fh — valor inicial
> No reset, o registrador de controle é **037Fh** = todas as exceções mascaradas + precisão dupla estendida + arredondamento ao mais próximo.

---

## Registrador de Tag (16 bits)

Armazena o **status de cada um dos 8 registradores** de dados: 2 bits por registrador = 16 bits.

| Valor (2 bits) | Significado |
|---|---|
| `00` | Válido — contém um número válido |
| `01` | Zero — contém zero |
| `10` | Especial — NaN, infinito ou desnormalizado |
| `11` | Vazio — registrador não utilizado |

---

## Ponteiros de Instrução e Dados (48 bits)

Para suporte a depuração e tratamento de exceções, a FPU mantém internamente:

| Campo | Tamanho | Conteúdo |
|---|---|---|
| **FIP** (FPU Instruction Pointer) | 48 bits | Selector de segmento (16 bits) + offset (32 bits) da última instrução FPU |
| **FDP** (FPU Data Pointer) | 48 bits | Selector de segmento (16 bits) + offset (32 bits) do último operando em memória |

---

## Registrador de Opcode (11 bits)

Armazena os **11 bits menos significativos do opcode** da última instrução FPU não-de-controle executada. Permite ao handler de exceção determinar qual instrução causou o erro.

---

## Registradores SIMD

As extensões SIMD (Single Instruction, Multiple Data) adicionam registradores progressivamente maiores, cada geração sobrepondo a anterior:

### Hierarquia de registradores

```
ZMM0  [512 bits] ──────────────────────────────────────── AVX-512
       └── YMM0  [256 bits] ─────────────────── AVX / AVX2
              └── XMM0  [128 bits] ── SSE / SSE2–4.2 / SSE4a
                     └── MM0  [64 bits] ── MMX (= R0 físico da FPU)
```

### Tabela de extensões

| Extensão | Registradores | Tamanho | Introduzida em |
|---|---|---|---|
| **MMX** | MM0–MM7 | 64 bits | Pentium MMX (1997) |
| **SSE** | XMM0–XMM7 (32 bits) / XMM0–XMM15 (64 bits) | 128 bits | Pentium III (1999) |
| **AVX / AVX2** | YMM0–YMM15 | 256 bits | Sandy Bridge (2011) |
| **AVX-512** | ZMM0–ZMM31 | 512 bits | Knights Landing (2016) |

### Disponibilidade por modo

| Modo | Registradores disponíveis |
|---|---|
| **32 bits** | MM0–MM7 · XMM0–XMM7 · YMM0–YMM7 · ZMM0–ZMM7 |
| **64 bits** | MM0–MM7 · XMM0–XMM15 · YMM0–YMM15 · ZMM0–ZMM31 |

> [!warning] AVX-512: ZMM0–ZMM15 vs. ZMM16–ZMM31
> - **ZMM0–ZMM15** são expansões dos YMM correspondentes (os 256 bits baixos de ZMM0 = YMM0)
> - **ZMM16–ZMM31** são registradores novos, sem equivalente YMM/XMM acessível separadamente

### Acesso individual (≠ pilha)

Diferentemente dos registradores x87, os registradores SIMD são **acessados individualmente por nome** (XMM0, YMM3, ZMM7 etc.) — não há ponteiro de pilha.

### Finalidade

| Extensão | Uso típico |
|---|---|
| MMX | Processamento de áudio/vídeo (8/16/32-bit inteiro empacotado) |
| SSE / SSE2–4 | Ponto flutuante simples e duplo empacotado; inteiros 128-bit |
| AVX / AVX2 | Operações vetoriais de alto desempenho (HPC, machine learning) |
| AVX-512 | Computação científica, IA, criptografia |

> [!info] Relação com [[Concorrência e Paralelismo#SIMD — Instrução única, múltiplos dados|SIMD]]
> AVX pode somar **8 pares de floats de 32 bits** em uma única instrução — o exemplo clássico de SIMD no nível de instrução.

---

## Ver também
- [[Instruções SIMD]] — todos os conjuntos MMX/SSE/AVX/AVX-512/AMX com detalhes de lançamento e registradores
- [[Processador]] — FPU como parte interna do processador moderno
- [[Registradores x86]] — catálogo geral; XCR0 controla estados SIMD (XSAVE)
- [[Modos de Operação x86]] — disponibilidade de XMM/YMM/ZMM por modo
- [[Concorrência e Paralelismo]] — SIMD como nível mais baixo de paralelismo
- [[Intel x86]] — linha histórica 8086→486DX com integração da FPU
- [[ISA]] — x87 e SSE/AVX como extensões da ISA x86
- [[Dados Binários]] — representação de ponto flutuante IEEE 754
