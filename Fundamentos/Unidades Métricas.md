---
title: Unidades Métricas
aliases:
  - prefixos métricos
  - SI
  - kibibyte
  - mebibyte
  - KiB
  - MiB
  - GiB
  - potências de dois
  - unidades de grandeza
tags:
  - computação/fundamentos
date: 2026-04-06
---

# Unidades Métricas

Unidades de grandeza são prefixos adicionados a uma unidade de medida para representar um fator multiplicador. Ex: 1 km = 1.000 m; 1 kg = 1.000 g.

---

## Prefixos decimais (SI — Sistema Internacional de Unidades)

Potências de 10, usadas no uso cotidiano e científico:

| Prefixo | Símbolo | Expoente | Valor |
|---|---|---|---|
| mili | m | 10⁻³ | 0,001 |
| micro | μ | 10⁻⁶ | 0,000001 |
| nano | n | 10⁻⁹ | 0,000000001 |
| pico | p | 10⁻¹² | 0,000000000001 |
| femto | f | 10⁻¹⁵ | |
| ato | a | 10⁻¹⁸ | |
| **quilo** | **k** | **10³** | **1.000** |
| **mega** | **M** | **10⁶** | **1.000.000** |
| **giga** | **G** | **10⁹** | **1.000.000.000** |
| **tera** | **T** | **10¹²** | **1.000.000.000.000** |
| peta | P | 10¹⁵ | 1.000.000.000.000.000 |
| exa | E | 10¹⁸ | |
| zeta | Z | 10²¹ | |
| yotta | Y | 10²⁴ | |

> [!tip] Capitalização SI
> Prefixos **maiores que 1** usam **maiúsculas** (M, G, T…). **Quilo é exceção: `k` minúsculo** (SI). "m" = mili; "μ" = micro.

---

## Prefixos binários (IEC — base 2)

Potências de 2, usadas para memória, processadores e armazenamento binário:

| Prefixo | Nome completo | Símbolo | Expoente | Valor exato |
|---|---|---|---|---|
| quibi | quibinário | Ki | 2¹⁰ | 1.024 |
| mebi | mebinário | Mi | 2²⁰ | 1.048.576 |
| gibi | gibinário | Gi | 2³⁰ | 1.073.741.824 |
| tebi | terabinário | Ti | 2⁴⁰ | 1.099.511.627.776 |
| pebi | pebinário | Pi | 2⁵⁰ | 1.125.899.906.842.624 |
| exbi | exbinário | Ei | 2⁶⁰ | 1.152.921.504.606.846.976 |
| zebi | zebinário | Zi | 2⁷⁰ | 1.180.591.620.717.411.303.424 |
| yobi | yobinário | Yi | 2⁸⁰ | 1.208.925.819.614.629.174.706.176 |

> [!note] Origem dos nomes
> Prefixos binários são derivados dos decimais com o sufixo "-bi" (de *binary*): ki**bi** ← ki**lo**; me**bi** ← me**ga**; gi**bi** ← gi**ga** etc. O símbolo sempre termina em "i" maiúsculo.

---

## O problema: dois sistemas em uso simultâneo

Em computação coexistem dois significados para os mesmos prefixos — e isso gera confusão:

| Prefixo | Decimal (SI) | Binário (IEC) | Diferença |
|---|---|---|---|
| Quilo (k/Ki) | 10³ = 1.000 | 2¹⁰ = 1.024 | +2,4% |
| Mega (M/Mi) | 10⁶ = 1.000.000 | 2²⁰ = 1.048.576 | +4,9% |
| Giga (G/Gi) | 10⁹ = 1.000.000.000 | 2³⁰ = 1.073.741.824 | +7,4% |
| Tera (T/Ti) | 10¹² | 2⁴⁰ = 1.099.511.627.776 | +10,0% |

---

## Três exceções: binário representado em decimal

Em três contextos, valores originalmente binários são expressos com definições **decimais**:

| Contexto | Unidade usada | Base | Exemplo |
|---|---|---|---|
| **Taxas de transferência** | Mbit/s, MB/s | Decimal (10⁶) | 10 Mbit/s = 10.000.000 bit/s |
| **Capacidade de armazenamento** | GB, TB (rótulo do fabricante) | Decimal (10⁹, 10¹²) | HD "2 TB" = 2.000.000.000.000 bytes |
| **Resolução de imagem** | megapixels | Decimal (10⁶) | 16 MP = 16.000.000 pixels |

### O caso do armazenamento — a armadilha mais comum

Fabricantes rotulam HDs, SSDs, pen drives e cartões com capacidades em **potências de 10** (decimal). O **Windows** reporta a capacidade em **potências de 2** (binário) — resultado: o SO sempre mostra um número menor.

| Rótulo do fabricante | Windows reporta | Por quê |
|---|---|---|
| HD de **2 TB** | **1,82 TB** | 2.000.000.000.000 ÷ 2⁴⁰ ≈ 1,82 |
| SSD de **256 GB** | **238,5 GB** | 256.000.000.000 ÷ 2³⁰ ≈ 238,5 |

> [!warning] Não é defeito nem fraude
> O disco foi formatado com capacidade total. O problema é que fabricante e SO usam definições diferentes para "GB". O usuário que acha que perdeu espaço está enganado — o espaço está lá, a diferença é de notação.

---

## Normas — histórico

| Ano | Organização | Norma | Conteúdo |
|---|---|---|---|
| 1998 | IEC | IEC 60027-2 | Cria os prefixos binários (Ki, Mi, Gi…) |
| 2002 | IEEE | IEEE 1541-2002 | Adota os prefixos IEC; padroniza B (byte) e b (bit) |
| 2008 | ISO/IEC | ISO/IEC 80000-13:2008 | Incorpora ao Sistema Internacional de Quantidades; define "bit" por extenso |

**Adoção atual:**
- ✅ Academia e Linux (distribuições como Ubuntu usam GiB/MiB)
- ⚠️ Fabricantes de memória e processadores → ainda usam GB/MB para base 2 (sem "i")
- ❌ Fabricantes de armazenamento → usam GB/TB para base 10 (sem "i")

---

## Regras de escrita (IEEE 1541-2002 / ISO/IEC 80000-13:2008)

### "k" minúsculo vs. "K" maiúsculo
- **`k`** (minúsculo) = quilo **decimal** = 1.000 → ex: `kB` = quilobyte = 1.000 bytes
- **`K`** (maiúsculo) = convenção histórica para **2¹⁰** = 1.024 → ex: `KB` = 1.024 bytes (uso informal)
- **`Ki`** = quibibyte **normalizado** = 1.024 bytes → ex: `KiB` (forma correta pela norma)

| Correto | Incorreto | Problema |
|---|---|---|
| `kB` | `KB` | K maiúsculo é ambíguo (binário ou decimal?) |
| `KiB` | `kiB` | "i" sempre em minúsculo no símbolo; "K" maiúsculo |
| `1 GB` | `1GB` | Obrigatório **espaço** entre número e unidade |
| `1 Gbit` ou `1 Gb` | `1Gbit` | Idem |

### Bit vs. Byte

| Símbolo | Representa | Norma |
|---|---|---|
| **B** (maiúsculo) | byte | IEEE 1541-2002 |
| **b** (minúsculo) | bit | IEEE 1541-2002 |
| "bit" (por extenso) | bit | ISO/IEC 80000-13:2008 ← adotada neste livro |

> [!warning] Erro de 8×
> Confundir `b` e `B` representa diferença de **8 vezes**. "100 Mbps" ≠ "100 MBps" (este é 8× maior). Fabricantes, mídia e operadoras cometem este erro com frequência.

### "Megas" e "gigas" — tecnicamente incorretos

O uso popular de "megas" e "gigas" (ex: "plano de 100 megas", "smartphone com 256 gigas") tem dois problemas:
1. **Plural:** unidades de grandeza **nunca vão ao plural**. Correto: "100 mega", não "100 megas"
2. **Unidade ausente:** "100 mega" *de quê*? MB? MiB? Mbit/s? MiB/s? Cada opção tem significado completamente diferente

> [!example] Ambiguidade de "100 megas"
> Poderia ser: 100 MB · 100 MiB · 100 Mbit · 100 Mibit · 100 MB/s · 100 MiB/s · 100 Mbit/s · 100 Mibit/s
> São até 8 interpretações distintas para a mesma expressão.

---

## Ver também
- [[Dados Binários]] — sistema binário, bits, representação
- [[Palavras Binárias]] — nibble, byte, word; abreviação bit (b) vs. byte (B)
- [[Memória]] — organização em bytes
- [[Memória de Massa]] — capacidade de armazenamento
