---
title: Microarquitetura P6 — Intel Família 6 (Parte 1)
tags:
  - hardware
  - arquitetura-x86
  - história
  - processadores
  - intel
aliases:
  - microarquitetura P6
  - Pentium Pro
  - Pentium II
  - Pentium III
  - Celeron P6
  - Xeon P6
  - família 6 Intel
  - FSB
  - BSB
---

# Microarquitetura P6 — Intel Família 6 (Parte 1)

Período: **1995–2001**. Processadores: Pentium Pro, Pentium II, Pentium III e derivados (Celeron, Xeon, Mobile).

> [!important] Base dos processadores Intel atuais
> A microarquitetura P6 foi o alicerce de todas as microarquiteturas Intel x86 posteriores (Core, Nehalem, Sandy Bridge… Golden Cove). Estudar P6 = entender a mecânica dos processadores modernos.

---

## Contexto

- Após o Pentium 4 (família F / microarquitetura Netburst, cap. 23) provar ser ineficiente, Intel voltou à linhagem P6
- Família 6 dividida em três capítulos pelo livro: este (P6 original 1995–2001), cap. 24 (revisões P6) e cap. 25 (gerações recentes)
- **Mesmo nome, microarquitetura diferente:** Pentium, Celeron e Xeon são nomes usados por processadores de microarquiteturas completamente distintas

---

## Técnicas da Microarquitetura P6

Todas as técnicas do [[Desempenho do Processador]]:
- Arquitetura híbrida CISC/RISC (µops de 118 bits)
- Execução fora de ordem
- Renomeamento de registradores (técnica **RRF** — alterada para PRF a partir do Sandy Bridge)
- Execução especulativa
- Arquitetura superescalar
- Previsão de desvios (BTB 512 entradas)
- Multiplicação de clock
- **Cache L2 integrado ao processador** (novidade principal — antes estava na placa-mãe)

---

## Integração do Cache L2 e Barramentos

### Antes (famílias 3–5)
```
Processador ←→ Barramento externo ←→ Cache L2 (placa-mãe) ←→ Chipset ←→ RAM
```

### Com P6
```
Núcleo ←[BSB]→ Cache L2 (dentro do processador)
Núcleo ←[FSB]→ Chipset (Ponte Norte) ←→ RAM
```

| Barramento | Nome completo | Função |
|-----------|--------------|--------|
| **FSB** (Front-Side Bus) | Barramento frontal / host bus / barramento local | Processador ↔ Chipset/RAM |
| **BSB** (Back-Side Bus) | Barramento traseiro | Núcleo ↔ Cache L2 (dedicado, maior largura de banda) |

---

## Pipeline P6 (12 estágios)

```
BUSCA 1 → BUSCA 2 → BUSCA 3 → BUSCA 4
→ DECODIFICAÇÃO 1 → DECODIFICAÇÃO 2
→ RENOMEAMENTO DE REGISTRADORES
→ BUFFER DE REORDENAMENTO
→ ENVIO (fora de ordem)
→ EXECUÇÃO (fora de ordem)
→ RETIRADA 1 → RETIRADA 2 (em ordem)
```

Divisão de etapas em vários estágios = **superpipelining** → mais instruções em paralelo + clock interno mais alto.

### Estágios de busca

| Estágio | Alias | O que faz |
|---------|-------|-----------|
| Busca 1 | "Próxima instrução" | Ponteiro da próxima instrução (cache L1-I, unidade de desvios ou interrupção) |
| Busca 2 | "Leitura L1-I" | Carrega linha de 32 bytes (256 bits) do cache L1 → buffer de pré-busca |
| Busca 3 | "Decodificação de comprimento" | Identifica limites das instruções em 16 bytes; registra desvios no BTB |
| Busca 4 | "Rotação/alinhamento" | Rotaciona bits para alinhamento correto ao decodificador |

> [!warning] LCP (Length-Changing Prefix)
> Instruções com prefixo de comprimento variável forçam algoritmo lento na busca → vários ciclos em vez de 1 para identificar a instrução.

### Decodificador de Instruções

| Tipo | Produz | Casos |
|------|--------|-------|
| **Simples** (×2) | 1 µop | Instruções comuns |
| **Complexo** (×1) | 1–4 µops | Instruções menos comuns |
| **Microcódigo (MIS + MSROM)** | >4 µops | Instruções raras e complexas |

Saída: até **3 µops/ciclo** → fila IDQ (6 µops na P6 original).

Instruções x86 têm tamanho variável; µops P6 têm sempre **118 bits**.

### Execução fora de ordem

| Estrutura | P6 original | Evolução |
|-----------|------------|---------|
| **Buffer de reordenamento (ROB)** | 40 µops | Ver tabela abaixo |
| **Estação de reserva (agendador)** | 20 µops | Ver tabela abaixo |
| **Portas de despacho** | 5 | Ver tabela abaixo |

- Busca/decodificação/retirada = **em ordem**
- Envio para execução = **fora de ordem** (estação de reserva escolhe µop executável)
- Retirada: µops saem do ROB **em ordem**, com atualização dos registradores lógicos

### Evolução do ROB / Estação de Reserva

| Microarquitetura | ROB | Estação de Reserva | Portas de Despacho |
|-----------------|-----|-------------------|-------------------|
| **P6** | 40 | 20 | 5 |
| Core | 96 | 32 | 6 |
| Nehalem | 128 | 36 | 6 |
| Sandy Bridge | 168 | 54 | 6 |
| Haswell | 192 | 60 | 8 |
| Broadwell | 192 | 64 | 8 |
| Skylake | 224 | 97 | 8 |
| Sunny Cove | 352 | — | 10 |
| Golden Cove | 512 | — | 12 |

---

## Unidades de Execução

### Pentium Pro

| Unidade | Porta | Ciclos/µop | Pipeline? | Função |
|---------|-------|-----------|-----------|--------|
| ALU (×2) | 0, 1 | 1 | Não | Instruções inteiros comuns |
| INT MUL | 0 | 4 (entrega 1/ciclo) | Sim | Multiplicação/divisão inteira |
| LEA | 1 | 1 | Não | Load Effective Address |
| SHIFT | 1 | 1 | Não | Rotação de bits inteiros |
| FP ADD | 0 | 3 (entrega 1/ciclo) | Sim | Soma/subtração FP |
| FP MUL | 0 | 5 (entrega 1/2 ciclos) | Sim | Multiplicação FP |
| FP DIV | 0 | dezenas | **Não** | Divisão e raiz quadrada FP — gargalo |
| Desvios | 1 | — | — | Limpa ROB de ramificações não tomadas |
| AGU carga | 2 | — | Sim | Gera endereço físico para leitura |
| AGU armazenamento | 3 (end.) + 4 (dados) | — | Sim | Gera endereço físico para escrita |

> [!note] Simplificação comum em bibliografias
> ALU + INT MUL + LEA + SHIFT → "unidade de inteiros". FP ADD + FP MUL + FP DIV → "FPU". Simplificação oculta que FP DIV não tem pipeline enquanto FP ADD e MUL têm.

### Pentium II — adições MMX

| Unidade | Porta | Pipeline? | Função |
|---------|-------|-----------|--------|
| SIMD ALU (×2) | 0, 1 | Não | Instruções MMX gerais |
| SIMD MUL | 0 | Sim (3 ciclos, 1/ciclo) | MMX multiplicação |
| SIMD SHIFT | 1 | Não | MMX rotação, pack/unpack |

### Pentium III — adições SSE

| Unidade | Pipeline? | Função |
|---------|-----------|--------|
| FP-SIMD MUL | Sim | SSE multiplicação, divisão, raiz, movimentação |
| FP-SIMD ADD | Sim | SSE soma, subtração, comparação, min/max, conversão |
| FP-SIMD SHUFFLE | Não | SSE lógicas, shuffle, desempacotamento, movimentação |
| FP-SIMD RECIP | Não | SSE estimativas recíprocas |

> [!warning] SSE 128 bits quebrado em 2×64 bits no Pentium III
> Caminhos de dados da P6 original são 64 bits. Instruções SSE de 128 bits = 2 µops de 64 bits. Corrigido na microarquitetura Core.

---

## Processadores

### Pentium Pro — 1995 (soquete 8)
- Servidores e workstations (não para usuário doméstico)
- L1: 16 KiB (8+8). L2: 256/512 KiB ou 1 MiB **no clock interno** (primeiro L2 on-die)
- Introduziu modo **PAE-36** (acesso a até 64 GiB)

### Pentium II — 1996–1998 (slot 1 / SC242)
- Versão desktop do Pentium Pro + MMX
- L1: 32 KiB (16+16). L2: 512 KiB a **½ clock interno** (no cartucho SEC, não no die)
- Chip + cache em placa de circuito → cartucho SEC
- Introduziu estado de economia **C3**

### Pentium II OverDrive — 1998
- Pentium II com pinagem soquete 8 (upgrade para placas Pentium Pro)

### Mobile Pentium
- Pentium II para portáteis (apesar do nome "Mobile Pentium")

### Pentium III — 1999–2001 (slot 1 ou soquete 370)
- Pentium II + SSE + novas unidades de execução
- L1: 32 KiB (16+16). L2: 256 KiB (mesmo clock) ou 512 KiB (½ ou mesmo clock)
- Número de série único → polêmica privacidade → removido

### Mobile Pentium III / III-M
- III: L2 256 KiB no clock interno
- III-M: L2 512 KiB no clock interno (mais rápido que III de mesmo clock)
- **III-M ≠ Pentium M** (Pentium M é capítulo 24)

### Celeron (P6)
- Versão de baixo custo derivada de Pentium II e Pentium III
- **Atenção:** nome Celeron foi usado por processadores de múltiplas microarquiteturas diferentes

### Mobile Celeron (P6)
- Celeron para portáteis baseado em Pentium II/III

### Pentium II Xeon — 1998 (slot 2 / SC330)
- Servidores e workstations
- L2: 512 KiB / 1 / 2 MiB **no clock interno** (diferença chave vs. Pentium II onde era ½)
- Introduziu modo **PSE-36** (acesso a até 64 GiB)
- Slot 2 incompatível com slot 1

### Pentium III Xeon (slot 2)
- Pentium III para servidores
- L2: 256 KiB / 512 KiB / 1 / 2 MiB no clock interno
- SMP até 4 processadores (8 nos modelos 2 MiB)
- Suporte a PAE-36 e PSE-36

---

## Ver também

- [[Desempenho do Processador]] — todas as técnicas P6 explicadas (OoO, especulativa, renomeamento, CISC/RISC)
- [[Cache]] — L2 integrado ao chip (BSB dedicado)
- [[Instruções SIMD]] — MMX (Pentium II), SSE (Pentium III)
- [[Paginação x86]] — PAE-36 (Pentium Pro), PSE-36 (Pentium II Xeon)
- [[Microarquitetura Core e Processadores Intel Família 6 Parte 2]] — evolução P6: Banias, Core, Penryn; LSD, macro-fusão, 128-bit SSE nativo
- [[Microarquitetura Netburst e Intel Família F]] — sucessor direto; hiperpipeline e fracasso térmico
- [[Processadores x86 Família 5]] — predecessores (Pentium clássico, K5, K6)
- [[Intel x86]] — linha histórica geral
