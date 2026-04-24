---
title: Latch e Flip-Flop
aliases:
  - latch
  - flip-flop
  - SR latch
  - D latch
  - D flip-flop
  - circuito sequencial
  - memória de 1 bit
  - edge-triggered
tags:
  - computação/arquitetura
  - computação/fundamentos
date: 2026-04-12
source: "Organização Estruturada de Computadores — Tanenbaum, Cap. 3"
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Latch e Flip-Flop

Circuitos sequenciais capazes de armazenar **1 bit**. Diferem de circuitos combinatórios porque a saída depende não apenas das entradas atuais, mas do **estado anterior** (memória).

→ Construídos com: [[Portas Lógicas]]  
→ Agrupados em: [[Registrador]] (n flip-flops = registrador de n bits)  
→ Implementam: [[RAM — Tecnologias e Módulos]] (SRAM usa flip-flops)

---

## Relações (SPO)

- SR latch → construído com → 2 portas NOR (ou 2 portas NAND)
- D flip-flop → captura → entrada D na borda do clock
- Flip-flop → compõe → [[Registrador]] (n flip-flops = n bits)
- SRAM → usa → flip-flop (4–6 transistores/bit) como célula
- Clock → sincroniza → captura de todos os flip-flops no circuito

---

## SR Latch (NOR)

Circuito com **retroalimentação**: saída Q realimentada nas entradas.

```
S ──┐
    NOR ── Q
Q̄ ─┘

R ──┐
    NOR ── Q̄
Q ──┘
```

| S | R | Q (próximo) | Q̄ |
|---|---|---|---|
| 0 | 0 | Q (mantém) | Q̄ (mantém) |
| 1 | 0 | 1 | 0 |
| 0 | 1 | 0 | 1 |
| 1 | 1 | **indefinido** | **indefinido** |

> [!warning] Estado proibido: S=R=1
> Com ambas entradas em 1, Q = Q̄ = 0 (viola a definição). Ao retornar S=R=0, o estado final depende de qual entrada desativa por último — indeterminado.

**Interpretação:**
- S=1 (Set): força Q=1
- R=1 (Reset): força Q=0
- S=R=0: latch mantém estado anterior (memória)

### SR Latch (NAND) — lógica invertida

Construído com NANDs; entradas **ativas em nível baixo**.

| S̄ | R̄ | Q |
|---|---|---|
| 1 | 1 | mantém |
| 0 | 1 | 1 (Set) |
| 1 | 0 | 0 (Reset) |
| 0 | 0 | **indefinido** |

---

## D Latch (Latch Transparente)

Resolve o problema do estado proibido do SR latch. Uma única entrada **D** + sinal **Enable (CLK)**.

```
D ─────┬── AND ──── S
       │               SR-Latch
NOT ───┴── AND ──── R
↑
CLK ──────────────────
```

| CLK | D | Q (próximo) |
|---|---|---|
| 1 | 0 | 0 |
| 1 | 1 | 1 |
| 0 | X | Q (mantém) |

**Latch transparente:** quando CLK=1, Q segue D continuamente. Quando CLK=0, Q mantém o último valor.

> [!note] Problema do latch transparente
> Se D muda enquanto CLK=1, Q muda imediatamente. Em circuitos síncronos, isso pode causar "transparência" indesejada: a saída muda antes do ciclo de clock terminar.

---

## D Flip-Flop (Edge-Triggered)

Captura D **somente na borda do clock** (transição 0→1 ou 1→0), eliminando o problema da transparência.

### Implementação Master-Slave

Dois D latches em cascata com clocks complementares:

```
D ── [Mestre (CLK=1)] ── [Escravo (CLK=0)] ── Q
                ↑                    ↑
               CLK               NOT(CLK)
```

1. CLK=0: Mestre **transparente** (captura D), Escravo **fechado** (mantém Q)
2. CLK=1: Mestre **fechado** (isola D), Escravo **transparente** (copia valor do Mestre para Q)

Resultado: Q muda apenas na **borda de descida** do clock (0→1 no Mestre → 1→0 propaga para Escravo).

Flip-flops de borda de subida (rising-edge) usam lógica invertida.

### Símbolo e notação

```
    ┌────┐
D ──│ D  │── Q
    │   >│── Q̄
CLK─│    │
    └────┘
```

O triângulo `>` indica edge-triggered. Bola (`○`) antes = borda de descida (falling-edge).

---

## Circuito de Clock

O clock é um sinal de onda quadrada que sincroniza todos os flip-flops do sistema.

```
   ┌─┐ ┌─┐ ┌─┐
───┘ └─┘ └─┘ └─
  ← período T →
```

- **Frequência**: f = 1/T (ex: 3 GHz → T ≈ 333 ps)
- **Geração**: oscilador a cristal de quartzo (referência estável) + **PLL** (Phase-Locked Loop) multiplica a frequência do cristal

> [!note] Por que cristal?
> Cristais de quartzo têm frequência de ressonância mecânica muito estável (tipicamente 25–100 MHz). A PLL multiplica essa frequência por um fator inteiro para atingir GHz.

---

## Registrador de n Bits

n flip-flops D compartilhando o mesmo clock:

```
D0 ──[FF]── Q0
D1 ──[FF]── Q1   ← todos os CLK conectados
D2 ──[FF]── Q2
D3 ──[FF]── Q3
     ↑
    CLK
```

Na borda do clock, todos os bits D[n-1:0] são capturados simultaneamente em Q[n-1:0].

Um **registrador de deslocamento** (shift register) encadeia as saídas: Q de cada flip-flop → D do próximo. Usado para conversão série/paralelo.

---

## SRAM — Memória com Flip-Flops

Cada célula SRAM = flip-flop de 4 ou 6 transistores MOSFET.

| Aspecto | Detalhe |
|---|---|
| Células/bit | 4–6 transistores |
| Refresh | Não necessário (flip-flop mantém estado) |
| Velocidade | Alta (comparada à DRAM) |
| Densidade | Baixa (muitos transistores/bit) |
| Uso | Cache L1/L2/L3 dentro do processador |

Ver [[RAM — Tecnologias e Módulos#SRAM vs DRAM]] para comparação com DRAM (capacitor + MOSFET).

---

## Ver também

- [[Portas Lógicas]] — NOR e NAND usados na construção de latches
- [[Registrador]] — n flip-flops formam registrador; papel no caminho de dados
- [[Circuitos Combinatórios]] — circuitos sem memória; latch/FF adicionam estado
- [[RAM — Tecnologias e Módulos]] — SRAM usa flip-flop; DRAM usa capacitor
- [[ULA]] — flip-flops armazenam operandos e resultado no caminho de dados
