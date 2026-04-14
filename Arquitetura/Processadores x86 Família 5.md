---
title: Processadores x86 — Família 5
tags:
  - hardware
  - arquitetura-x86
  - história
  - processadores
aliases:
  - família 5
  - Pentium
  - Pentium MMX
  - K5
  - K6
  - K6-2
  - K6-III
  - 6x86
  - Nx586
---

# Processadores x86 — Família 5

Período: **1993–1999**. Responde ao `CPUID` como família 5.

> [!note] Família ≠ geração por fabricante
> AMD chama K5 de 5ª geração e K6 de 6ª geração — ambos são família 5. "Família" (CPUID) é mais confiável que "geração".

---

## Contexto histórico

- Intel passou a usar **nomes** (Pentium) em vez de números → proteção de marca registrada impossível com números
- Cada fabricante desenvolveu microarquitetura **própria** (não mais engenharia reversa)
- Todos usavam o mesmo soquete do Pentium (exceto NexGen) → upgrade fácil
- Consolidação: AMD comprou NexGen (1996); Cyrix → National Semi (1997) → VIA (1999); IDT/Centaur → VIA (1999)

---

## Linha do Tempo

```
1993  Pentium clássico, Nx586
1995  K5
1996  6x86, K6
1997  Pentium MMX, 6x86MX
1998  MII, K6-2
1999  K6-III
```

---

## Intel

### Pentium (clássico / "Pentium 1") — 1993

Mesmo modo protegido 32 bits do 386 + FPU integrada do 486. Novidades:

| Recurso | Detalhe |
|---------|---------|
| Barramento de dados | **64 bits** (dobro do 486) |
| Cache L1 | **16 KiB dividido**: 8 KiB instruções + 8 KiB dados |
| Previsão de desvios | Sim — BTB |
| Superescalar | **2 canalizações** |

> [!tip] Largura de banda memória
> 486 @ 33 MHz externo + 32 bits = 132 MB/s. Pentium @ 66 MHz + 64 bits = **528 MB/s** — 4× maior.

"Pentium" como marca persiste até hoje na Intel, mas processadores atuais com esse nome não têm relação com o original.

---

### Pentium MMX — 1997

Pentium clássico + instruções MMX + cache L1 expandido:

| Atributo | Pentium | Pentium MMX |
|----------|---------|-------------|
| Cache L1 | 16 KiB | **32 KiB** (16 KiB I + 16 KiB D) |
| MMX | Não | Sim |

Cache maior = mais rápido mesmo em programas **sem** instruções MMX.

---

### Pentium OverDrive

Pentium com **pinagem de 486** — upgrade sem trocar placa-mãe. Muito mais lento que o Pentium real (barramento externo de 486, não 64 bits). Cache L1: 32 KiB (16+16).

---

## Cyrix

### 6x86 — 1996

Compatível com soquete Pentium. Incorporou recursos que a Intel só adotou na família 6:

- Cache L1 16 KiB **unificado**
- Superescalar 2 canalizações
- **Execução fora de ordem**
- **Arquitetura híbrida CISC/RISC**
- **Renomeamento de registradores**
- **Execução especulativa**

> [!warning] Nomenclatura PR — enganosa
> Cyrix rotulava com "PR" (Performance Rating) em vez do clock real. 6x86-PR166+ rodava a **133 MHz** mas era vendido como "equivalente a Pentium 166". Metodologia favorecia o Cyrix.

### 6x86MX / MII — 1997–1998

6x86 + cache L1 **64 KiB** + instruções **MMX**. Renomeado MII a partir do modelo PR300.

---

## NexGen

### Nx586 — 1993

Baixíssima adoção no mercado. Relevância histórica: projeto Nx686 foi comprado pela AMD e relançado como K6.

---

## AMD

### K5 — 1995

Primeiro processador original da AMD (não cópia de Intel). Soquete Pentium.

| Recurso | Detalhe |
|---------|---------|
| Cache L1 | **24 KiB**: 16 KiB instruções + 8 KiB dados |
| Superescalar | **6 canalizações**: 2× inteiros + 1× FPU + 1× desvios + 1× carga + 1× armazenamento |
| Previsão de desvios | Sim |
| CISC/RISC híbrido | Sim |
| Execução fora de ordem | Sim |
| Renomeamento de registradores | Sim |
| Execução especulativa | Sim |

Também usou nomenclatura PR (igual Cyrix). Exceção: K5-PR75 a K5-PR100 — número = clock real.

---

### K6 — 1996

Projeto Nx686 da NexGen renomeado (AMD comprou NexGen em 1996). **Não** derivado do K5.

| Recurso | Detalhe |
|---------|---------|
| Cache L1 | **64 KiB**: 32 KiB I + 32 KiB D |
| Superescalar | **7 canalizações**: 2× inteiros + 1× FPU + 1× MMX + 1× desvios + 1× carga + 1× armazenamento |
| MMX | Sim |
| + todos os recursos do K5 | Sim |

---

### K6-2 — 1998

K6 melhorado:
- MMX superescalar **dupla canalização** (2 instruções MMX em paralelo)
- Introdução das instruções **3DNow!** (ver [[Instruções SIMD]])

---

### K6-III — 1999

K6-2 + cache L2 de **256 KiB integrado ao chip**, rodando no clock interno do processador.

Cache externo da placa-mãe continua usável → passa a ser chamado **L3**.

Hierarquia resultante: L1 (chip) → L2 (chip, 256 KiB) → L3 (placa-mãe) — primeiro processador x86 com 3 níveis de cache.

---

## Tabela Comparativa — Cache L1

| Processador | L1 Total | Divisão |
|------------|---------|---------|
| Pentium | 16 KiB | 8+8 |
| Pentium MMX | 32 KiB | 16+16 |
| Pentium OverDrive | 32 KiB | 16+16 |
| 6x86 | 16 KiB | unificado |
| 6x86MX / MII | 64 KiB | unificado |
| K5 | 24 KiB | 16+8 |
| K6 | 64 KiB | 32+32 |
| K6-2 | 64 KiB | 32+32 |
| K6-III | 64 KiB | 32+32 |

---

## Ver também

- [[Processadores x86 Famílias 1-4]] — contexto de origem + família 4 (486)
- [[Desempenho do Processador]] — superescalar, OoO, especulativa, renomeamento, CISC/RISC híbrido
- [[Instruções SIMD]] — MMX (Pentium MMX, K6), 3DNow! (K6-2)
- [[Cache]] — L1 dividido introduzido no Pentium; L3 surgiu com K6-III
- [[Processadores AMD Família K7]] — sucessores AMD: Athlon, Athlon XP, Duron, Sempron
- [[Microarquitetura P6 e Processadores Intel Família 6 Parte 1]] — sucessores Intel: Pentium Pro, II, III
- [[Intel x86]] — linha histórica geral
