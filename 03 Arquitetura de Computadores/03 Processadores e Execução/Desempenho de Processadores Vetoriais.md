---
title: Desempenho de Processadores Vetoriais
tags:
  - hardware
  - arquitetura
  - DLP
  - vetor
  - desempenho
aliases:
  - vector performance
  - Tn formula
  - VMIPS performance
  - R infinito vetorial
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Desempenho de Processadores Vetoriais

Análise quantitativa de [[Arquitetura Vetorial|processadores vetoriais]]. Complementa o modelo de chimes com overhead de start-up e strip-mining.

Fonte: P&H "Computer Architecture: A Quantitative Approach", Apêndice G.

## Fórmula Tn — modelo completo

$$T_n = \left\lceil \frac{n}{MVL} \right\rceil \times (T_{loop} + T_{start}) + n \times T_{chime}$$

| Parâmetro | Descrição | VMIPS |
|---|---|---|
| $T_{chime}$ | Nº de convoys no loop | depende do código |
| $T_{start}$ | Soma dos start-up times de cada convoy | soma por sequência |
| $T_{loop}$ | Overhead escalar de strip-mining por bloco | 15 ciclos |
| $MVL$ | Comprimento máximo do vetor | 64 elementos |

### Start-up times no VMIPS (= Cray-1)

| Unidade | Start-up (ciclos) |
|---|---|
| Load / Store | 12 |
| FP multiply | 7 |
| FP add | 6 |
| FP divide | 20 |

### Timing por convoy (sequência LV → MULVS → ADDV → SV, n elementos)

| Convoy | Instrução | Início | Primeiro resultado | Último resultado |
|---|---|---|---|---|
| 1 | LV | 0 | 12 | $12 + n$ |
| 2 | MULVS.D | $12 + n$ | $12 + n + 12$ | $23 + 2n$ |
| 3 | ADDV.D | $24 + 2n$ | $24 + 2n + 6$ | $29 + 3n$ |
| 4 | SV | $30 + 3n$ | $30 + 3n + 12$ | $41 + 4n$ |

### Exemplo: A = B × s, n=200

Três convoys dependentes (LV → MULVS.D → SV):

$$T_{start} = 12 + 7 + 12 = 31 \quad T_{chime} = 3$$
$$T_{200} = 4 \times (15 + 31) + 200 \times 3 = 184 + 600 = 784 \text{ ciclos}$$
$$\text{tempo/elemento} = 784/200 = 3{,}9 \text{ ciclos} \quad \text{(chimes: 3 — overhead real: +30\%)}$$

## Medidas de desempenho vetorial

### $R_\infty$ — MFLOPS para vetor infinito

$$R_\infty = \lim_{n \to \infty} \frac{\text{FLOPS/iteração} \times \text{clock rate}}{T_n / n}$$

Mede pico real (não teórico) — inclui start-up e strip-mining amortizados.

### $N_{1/2}$ — vetor mínimo para $R_\infty / 2$

Mede o impacto do overhead de start-up. Quanto menor, melhor tolerância a vetores curtos.

### $N_v$ — vetor mínimo para superar escalar

Break-even: ponto a partir do qual modo vetorial é mais rápido que escalar.

## DAXPY no VMIPS (500 MHz) — análise completa

### Convoys (com chaining)

| Convoy | Instruções | Observação |
|---|---|---|
| 1 | LV X + MULVS.D (a×X) | chained: load → multiply |
| 2 | LV Y + ADDV.D (a×X + Y) | chained: 2º load → add |
| 3 | SV resultado | — |

$$T_{chime} = 3 \quad T_{start} = 12 + 7 + 12 + 6 + 12 = 49$$

### Desempenho de pico ($R_\infty$)

$$T_n \approx 4n + 64 \quad \Rightarrow \quad R_\infty = \frac{2 \times 500}{4} = 250 \text{ MFLOPS}$$

### Sustentado (Linpack — comprimento médio ≈ 66)

$$T_{66} = 2 \times (15 + 49) + 66 \times 3 = 326 \text{ ciclos}$$
$$R_{66} = \frac{2 \times 66 \times 500}{326} \approx 202 \text{ MFLOPS}$$

### $N_{1/2}$ e $N_v$ para DAXPY no VMIPS

$$N_{1/2} = 13 \text{ elementos} \quad N_v = 2 \text{ elementos}$$

$N_v = 2$: vetorial bate escalar com apenas 2 elementos — overhead de strip-mining é pequeno relativo ao ganho.

### Melhorias arquiteturais

| Configuração | $T_{66}$ | $R_\infty$ | Ganho vs. base |
|---|---|---|---|
| Base (1 pipeline memória) | 326 | 250 MFLOPS | — |
| 3 pipelines de memória | 194 | 750 MFLOPS | 1,7× (Amdahl: pico era 3×) |
| 3 pipelines + sobreposição total | 130 | 1000 MFLOPS | 2,5× |

> [!note] Amdahl no contexto vetorial
> 3 pipelines de memória → melhoria teórica de 3× nos chimes.
> Melhoria real em $T_{66}$: apenas 1,7×. Overhead de start-up e loop domina.

## Chaining — timing detalhado

Sequência dependente MULV → ADDV:

| Modo | Ciclos totais (n=64) | FLOPS/ciclo |
|---|---|---|
| Sem chaining | 141 | 0,9 |
| Com chaining | 77 | 1,7 |

> [!note]
> Chaining elimina chimes extras (operações dependentes ficam no mesmo convoy) mas não elimina start-up. O timing real do convoy inclui start-up (7 + 6 ciclos).

## Dead time entre instruções vetoriais

Algumas implementações inserem **dead time** entre instruções ao mesmo functional unit para simplificar a lógica de controle.

**Exemplo — Cray C90** (2 lanes, dead time = 4 ciclos, vetor máximo = 128):

$$\text{eficiência} = \frac{64}{64 + 4} = 94{,}1\%$$

Com 16 lanes (vetor de 128 → 8 elementos/lane):

$$\text{eficiência} = \frac{8}{8 + 4} = 66{,}6\%$$

> [!warning] Dead time × múltiplas lanes
> Mais lanes → vetor mais curto por lane → dead time é proporção maior do tempo útil.
> Pipelined instruction start-up (sobreposição entre instruções) torna-se crítico com muitas lanes.

## Tailgating

Técnica para esconder overhead de strip-mining sobrepondo duas instâncias do loop (usada no Cray-2):

- Dois blocos de strip-mining executam simultaneamente
- Requer técnicas para evitar hazards WAR (leitura/escrita de mesmo registrador vetorial em instâncias diferentes)
- Alternativa: desenrolar loop externo com conjuntos de registradores distintos
- Resultado: $T_{start}$ e $T_{loop}$ vistos apenas uma vez por sequência vetorial, não por bloco

Com tailgating e 3 pipelines: $T_n = n + 64$ → $R_\infty = 1000$ MFLOPS (DAXPY, VMIPS 500 MHz).

## Efetividade de compiladores vetorizadores

Teste com 100 kernels FORTRAN projetados para vetorização (todos vetorizáveis manualmente):

| Processador | Compilador | Completo | Parcial | Não |
|---|---|---|---|---|
| Cray X-MP | CFT77 V3.0 | 69 | 3 | 28 |
| Cray X-MP | CFT V1.1 | 50 | 1 | 49 |
| Convex C-series | FC | 69 | 5 | 26 |
| NEC SX/2 | FORTRAN77/SX | 66 | 5 | 29 |
| Hitachi S810 | FORT77/HAP | 67 | 4 | 29 |
| CDC CYBER 205 | VAST-2 | 62 | 5 | 33 |

Dois compiladores para o mesmo Cray X-MP: diferença de 19 pontos percentuais em vetorização completa.

## Relações (SPO)

- $T_n$ → modela → execução vetorial com start-up + strip-mining
- $R_\infty$ → define → pico real (vs. teórico por chimes)
- $N_{1/2}$ → mede → sensibilidade ao overhead de start-up
- $N_v$ → define → break-even vetorial vs. escalar
- Tailgating → elimina → overhead de strip-mining via sobreposição de iterações
- Dead time → degrada → eficiência especialmente com muitas lanes
- Chaining → mantém → operações dependentes no mesmo convoy

## Ver também

- [[Arquitetura Vetorial]] — conceitos: convoy, chime, chaining, strip-mining, lanes
- [[Sistema de Memória Vetorial]] — bancos, latência, conflitos de stride
- [[Cray X1]] — processador vetorial moderno: 2048 requests in-flight, MSP
- [[Modelo Roofline]] — análise gráfica compute vs. memória
