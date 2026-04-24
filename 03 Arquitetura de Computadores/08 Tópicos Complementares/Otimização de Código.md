---
title: Otimização de Código
tags:
  - arquitetura
  - desempenho
  - programação
aliases:
  - Otimização de Programas
  - otimização de desempenho
  - CPE
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Otimização de Código

Técnicas para extrair máximo desempenho de programas, agindo em dois níveis: eliminar trabalho desnecessário (independente do hardware) e explorar as capacidades do microprocessador ([[Execução Fora de Ordem]], [[Pipeline]], unidades funcionais).

---

## CPE — Ciclos por Elemento

Métrica que expressa custo de laços iterativos como coeficiente linear:

$$T(n) = \text{overhead} + \text{CPE} \cdot n$$

- Determinado ajustando linha reta ao tempo medido em função de $n$
- Independe da frequência do clock → compara eficiência de implementações
- **Latency bound**: CPE mínimo imposto pela latência da operação dominante
- **Throughput bound**: CPE mínimo imposto pela capacidade das unidades funcionais

### Unidades Funcionais — Intel Core i7 Haswell (referência)

| Operação | Latência (ciclos) | Issue time | Capacidade |
|---|---|---|---|
| Adição inteira | 1 | 1 | 4 |
| Multiplicação inteira | 3 | 1 | 1 |
| Adição float/double | 3 | 1 | 1 |
| Multiplicação float/double | 5 | 1 | 2 |
| Divisão (int/float) | 3–30 | 3–30 | 1 |
| Load (L1 hit) | 4 | 1 | 2 |
| Store | 1 | 1 | 1 |

> [!note] Issue time vs latência
> **Latência**: ciclos para completar a operação. **Issue time**: ciclos mínimos entre operações independentes consecutivas. Issue time = 1 significa unidade totalmente pipelined.

---

## Bloqueadores de Otimização

Aspectos do programa que impedem o compilador de aplicar otimizações seguras.

### Aliasing de Memória

Dois ponteiros distintos apontando para o mesmo endereço. O compilador não pode assumir que não há aliasing:

```c
// twiddle1: 6 refs de memória (pode ser otimizado para twiddle2 SE xp != yp)
void twiddle1(long *xp, long *yp) {
    *xp += *yp;
    *xp += *yp;
}
// twiddle2: 3 refs de memória (DIFERENTE quando xp == yp: ×4 vs ×3)
void twiddle2(long *xp, long *yp) {
    *xp += 2 * *yp;
}
```

Compilador assume pior caso (aliasing possível) → não transforma `twiddle1` em `twiddle2`.

> [!tip] Solução para o programador
> Usar variáveis locais para armazenar valores intermediários. O compilador pode manter variáveis locais em registradores, eliminando leituras/escritas redundantes na memória.

### Chamadas de Função com Efeitos Colaterais

O compilador não pode remover chamadas ou reordená-las sem saber se há side effects:

```c
// func1 chama f() 4 vezes — compilador NÃO converte para func2
// porque f() pode ter side effect (ex: contador global)
long func1() { return f() + f() + f() + f(); }
long func2() { return 4 * f(); }
```

Solução: **inlining** (quando compilador tem acesso ao código de `f`).

---

## Otimizações Básicas

### Code Motion — Mover Invariantes para Fora do Laço

Computação que retorna o mesmo valor a cada iteração deve ser calculada uma vez:

```c
// Ruim: strlen() chamada a cada iteração → O(n²) para string de comprimento n
for (i = 0; i < strlen(s); i++) ...

// Bom: O(n)
long len = strlen(s);
for (i = 0; i < len; i++) ...
```

> [!warning] Compiladores são conservadores
> O compilador não pode verificar que `strlen(s)` é invariante (os chars mudam). Cabe ao programador mover a chamada.

### Acumular em Variável Local

Elimina leitura e escrita do acumulador na memória a cada iteração:

```c
// combine3: lê e escreve *dest a cada iteração (2 refs/iter)
for (i = 0; i < n; i++) *dest = *dest OP data[i];

// combine4: acumula em registrador, escreve apenas uma vez
data_t acc = IDENT;
for (i = 0; i < n; i++) acc = acc OP data[i];
*dest = acc;
```

O compilador **não pode** fazer essa transformação automaticamente: se `dest` for alias de algum elemento de `data`, os resultados diferem.

**Ganho medido (Haswell, double × mul):** CPE de 9,02 (combine3) → 5,01 (combine4).

---

## Modelo de Processador e Caminho Crítico

### Grafo de Fluxo de Dados

Representação visual das dependências de dados entre operações de um laço. Classifica registradores em:

| Categoria | Descrição |
|---|---|
| **Read-only** | Usados como fonte, não modificados no laço |
| **Write-only** | Destino de operações, não lidos no laço |
| **Local** | Atualizados e usados dentro do laço, sem dependência entre iterações |
| **Loop** | Produzidos em uma iteração, consumidos na próxima (formam cadeias críticas) |

### Caminho Crítico

Cadeia mais longa de dependências de dados entre registradores de laço. Determina o **latency bound**:

- `combine4` com `double ×`: caminho crítico = `n` multiplicações de latência 5 → CPE = 5,00
- A cadeia de incremento do ponteiro (latência 1) corre em paralelo, sem impacto

> [!example] CPE = latency bound
> Para qualquer operação com latência $L$, `combine4` atinge CPE = $L$, pois o acumulador forma uma cadeia de $n$ operações em sequência.

---

## Paralelismo a Nível de Instrução (ILP)

### Loop Unrolling k×1

Reduz overhead de controle do laço processando $k$ elementos por iteração, mas acumulando em **uma única variável**:

```c
// 2×1: duas operações por iteração, um único acc
for (i = 0; i < limit; i += 2) {
    acc = (acc OP data[i]) OP data[i+1];
}
```

**Resultado:** elimina overhead de loop (melhora CPE de adição inteira de 1,27 → 1,01). Para operações com latência > 1, o caminho crítico continua sendo $n$ operações → **sem melhora além do latency bound**.

### Múltiplos Acumuladores k×k

Divide os elementos entre $k$ acumuladores independentes; combina ao final:

```c
// 2×2: dois acumuladores paralelos
data_t acc0 = IDENT, acc1 = IDENT;
for (i = 0; i < limit; i += 2) {
    acc0 = acc0 OP data[i];
    acc1 = acc1 OP data[i+1];
}
*dest = acc0 OP acc1;
```

**Por que funciona:** os dois caminhos de multiplicação são **independentes** — cada acumulador tem sua própria cadeia de dependências com $n/2$ operações → CPE ≈ $L/2$.

**Requisito para atingir throughput bound:** $k \geq C \cdot L$, onde $C$ = capacidade da unidade e $L$ = latência. Ex: `float ×` exige $k \geq 2 \times 5 = 10$.

> [!warning] Validade para inteiros vs ponto flutuante
> Para inteiros: multiplicação e adição são **associativas** → `combine6` produz resultado idêntico a `combine4`.
> Para ponto flutuante: **não é associativo** → resultados podem diferir por arredondamento. Em geral aceitável para aplicações práticas.

### Transformação de Reassociação k×1a

Altera a ordem das operações por mudança de parênteses, criando paralelismo com um único acumulador:

```c
// combine5 (k×1): acc forma cadeia completa de n operações
acc = (acc OP data[i]) OP data[i+1];

// combine7 (k×1a): apenas metade das operações na cadeia do acc
acc = acc OP (data[i] OP data[i+1]);
//              ^-- independente do acc; pode executar em paralelo
```

**Resultado:** caminho crítico com $n/2$ operações → CPE ≈ $L/2$, idêntico ao k×k mas com um único acumulador.

| Transformação | CPE (double ×) | Observação |
|---|---|---|
| Sem unrolling (`combine4`) | 5,01 | latency bound |
| k×1 (`combine5`) | 5,01 | sem melhora além do latency bound |
| k×k (`combine6`) | 2,51 | 2 caminhos críticos independentes |
| k×1a (`combine7`) | 2,51 | 1 caminho crítico reduzido pela metade |

---

## Fatores Limitantes

### Register Spilling

Quando $k$ acumuladores supera o número de registradores disponíveis, o compilador armazena variáveis temporárias na pilha:

```asm
; 10×10 (dentro dos registradores): acc em %xmm0
vmulsd (%rdx), %xmm0, %xmm0

; 20×20 (register spilling): acc na pilha
vmovsd 40(%rsp), %xmm0
vmulsd (%rdx),   %xmm0, %xmm0
vmovsd %xmm0,    40(%rsp)
```

Spilling elimina o benefício dos acumuladores paralelos. x86-64 tem 16 registradores inteiros e 16 YMM → maioria dos laços atinge o throughput bound antes de spill.

### Desvios e Código Condicional

Ver [[Previsão de Desvio]] para penalidades.

Estratégias para código com condicionais:

1. **Desvios previsíveis** (ex: teste de limite de laço): custo desprezível — penalidade ocorre apenas nas últimas iterações.

2. **Desvios imprevisíveis** (dados arbitrários): usar **conditional moves** — computa ambos os ramos e seleciona com `cmov`, sem branch:

```c
// Imperativo: gera branch — CPE ~13,5 para dados aleatórios
if (a[i] > b[i]) { swap; }

// Funcional: gera conditional move — CPE ~4,0 independente dos dados
long min = a[i] < b[i] ? a[i] : b[i];
long max = a[i] < b[i] ? b[i] : a[i];
a[i] = min; b[i] = max;
```

### Dependência Escrita/Leitura de Memória

Quando um load lê endereço escrito por store anterior na mesma iteração, forma-se cadeia crítica:

```c
// write_read com src == dst: cadeia store→load→add por iteração (~7 ciclos)
*dst = val;
val = (*src) + 1;  // lê o que acabou de escrever → serial

// write_read com src != dst: load independente do store → CPE ~1,3
```

O processador detecta o conflito via **store buffer**: a load unit verifica endereços pendentes no buffer de stores. Se houver match, deve aguardar o dado ser depositado.

**Regra geral:** manter valores em variáveis locais (registradores) quando possível para evitar read-after-write via memória.

---

## Profiling

Ferramenta `gprof` (Unix) para identificar gargalos:

```bash
gcc -Og -pg prog.c -o prog   # 1. compilar com instrumentação
./prog arquivo.txt            # 2. executar (gera gmon.out)
gprof prog                   # 3. analisar
```

Saída: tempo por função (%) + contagem de chamadas + grafo de chamadas.

> [!tip] Amdahl aplicado a profiling
> Otimizar funções que consomem >50% do tempo tem impacto significativo. Otimizar funções com <5% raramente muda o tempo total. Ver [[Lei de Amdahl]].

---

## Localidade e Cache

### Princípio da Localidade

Programas com boa localidade têm miss rates menores → rodam mais rápido. Todos os níveis do sistema de memória são projetados para explorar localidade.

| Tipo | Definição | Exemplo |
|---|---|---|
| **Temporal** | Posição acessada → acessada novamente em breve | Variável acumuladora de laço |
| **Espacial** | Posição acessada → posições próximas acessadas em breve | Varredura sequencial de array |

**Regras simples:**
- Variáveis repetidamente acessadas no laço → bom temporal (compilador as mantém em registradores)
- Padrões stride-1 → bom espacial (cache carrega bloco de 64 B e serve vários acessos)
- Laços têm boa localidade tanto temporal quanto espacial para instruction fetches

### Padrões de Stride

**Stride-k** = acessar cada k-ésimo elemento de array contíguo. Miss rate estimada (cache fria):

$$\text{miss rate} \approx \min\!\left(1,\ \frac{k \cdot \text{sizeof}(T)}{B}\right)$$

- Stride-1: uma miss a cada $B/\text{sizeof}(T)$ acessos (ex: com B=64 e int=4 → 1 miss a cada 16 acessos = 6,25%)
- Stride-k grande: cada acesso pode ser miss → throughput cai para velocidade da RAM

### Arrays 2D em C — Row-Major

C armazena arrays em **row-major order**. Acessar por coluna cria stride-N:

```c
// stride-1 — boa localidade espacial
for (i = 0; i < M; i++)
    for (j = 0; j < N; j++)
        sum += a[i][j];

// stride-N — cada acesso pula N ints → miss rate ~100% para N grande
for (j = 0; j < N; j++)
    for (i = 0; i < M; i++)
        sum += a[i][j];
```

Diferença medida: **25× mais lento** na versão coluna-por-coluna (Core i7, CS:APP §6.5).

### Multiplicação de Matrizes — Impacto da Ordem dos Laços

Para $C = A \times B$ com três laços aninhados ($i$, $j$, $k$), a ordem determina o padrão de acesso ao inner loop:

| Classe (inner loop) | Misses/iteração | Arrays acessados no inner |
|---|---|---|
| AB (ijk, jik) | 1,25 | linha de A (stride-1) + coluna de B (stride-n) |
| AC (jki, kji) | 2,00 | coluna de A + coluna de C (stride-n em ambas) |
| BC (kij, ikj) | 0,50 | linha de B + linha de C (stride-1 em ambas) ← **melhor** |

Core i7: versão mais rápida ~**40× mais rápida** que a mais lenta para matrizes grandes.

---

## Resumo — Sequência de Otimização

| Nível | Técnica | Requisito |
|---|---|---|
| 1 | Algoritmo e estrutura de dados | — |
| 2 | Remover chamadas e cálculos desnecessários do laço (code motion) | — |
| 3 | Acumular em variável local (eliminar refs de memória) | sem aliasing |
| 4 | Loop unrolling k×1 | — |
| 5 | Múltiplos acumuladores k×k | operação associativa ou tolerância a reordenamento |
| 6 | Reassociação k×1a | idem |
| 7 | Stride-1 nos inner loops; transpor laços se necessário | — |
| 8 | Instruções SIMD (AVX) | ver [[Instruções SIMD]] |

---

## Ver também

- [[Execução Fora de Ordem]] — modelo de processador que executa as operações acima em paralelo
- [[Pipeline]] — base do ILP
- [[Previsão de Desvio]] — penalidades de misprediction e conditional moves
- [[Desempenho do Processador]] — visão geral de técnicas de desempenho do hardware
- [[Cache]] — organização formal (S,E,B,m), tipos de miss, métricas
- [[Hierarquia de Memória]] — memory mountain; por que localidade importa
- [[Lei de Amdahl]] — guia para focar esforços de otimização
- [[Instruções SIMD]] — vetor de dados para paralelismo adicional (~4–8× sobre scalar)
- [[Concorrência e Paralelismo]] — ILP e paralelismo de dados
