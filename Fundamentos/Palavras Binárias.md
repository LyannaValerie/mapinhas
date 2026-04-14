---
title: Palavras Binárias
aliases:
  - palavra binária
  - nibble
  - byte
  - octeto
  - word
  - double word
  - dword
  - quad word
  - qword
  - MSB
  - LSB
  - byte mais significativo
  - byte menos significativo
  - unsigned
tags:
  - computação/fundamentos
date: 2026-04-06
---

# Palavras Binárias

## Definição
Um número binário é também chamado **palavra binária**. Palavras recebem nomes padronizados conforme o número de bits — e a quantidade de valores distintos representáveis é 2ⁿ, onde n é o número de bits.

## Nomenclatura padrão

| Nome | Bits | Variações (2ⁿ) | Faixa unsigned |
|---|---|---|---|
| **Nibble** | 4 | 2⁴ = 16 | 0–15 |
| **Byte** (ou **octeto**) | 8 | 2⁸ = 256 | 0–255 |
| **Word** | 16 | 2¹⁶ = 65.536 | 0–65.535 |
| **Double Word (DWord)** | 32 | 2³² = 4.294.967.296 | 0–4.294.967.295 |
| **Quad Word (QWord)** | 64 | 2⁶⁴ ≈ 1,8 × 10¹⁹ | 0–18.446.744.073.709.551.615 |

**Fórmula do máximo unsigned:** $\text{máximo} = 2^n - 1$

Por padrão, a representação binária básica (**unsigned** = sem sinal) representa apenas inteiros não negativos, de 0 a 2ⁿ − 1. Ver [[Sistemas de Representação]] para representações com sinal.

## Byte vs. Octeto

- **Byte** — tecnicamente, qualquer grupo de bits adjacentes. Por motivos históricos (décadas de computação com dados de 8 bits), o byte padrão tem 8 bits.
- **Octeto** — termo tecnicamente preciso para um grupo de **exatamente 8 bits**. Preferido em:
  - Redes de computadores (RFC/IETF)
  - Idiomas que evitam anglicismos (ex: francês — *octet*, não *byte*)
- Na prática: **byte e octeto são sinônimos** para 8 bits.

> [!info] Origem da ambiguidade
> "Byte" não tem tamanho fixo em sua definição original — mas na prática nunca se viu byte ≠ 8 bits desde o surgimento dos PCs. O termo "octeto" elimina qualquer ambiguidade.

## Palavras maiores expressas em bytes

Como o byte é a unidade base, palavras maiores são frequentemente descritas em bytes:

| Palavra | Equivalência em bytes |
|---|---|
| Word (16 bits) | 2 bytes |
| DWord (32 bits) | 4 bytes |
| QWord (64 bits) | 8 bytes |

Dados são organizados, transmitidos e armazenados em **grupos de bytes** — daí a ubiquidade do byte como unidade fundamental.

## MSB e LSB — Byte mais e menos significativo

Em palavras com múltiplos bytes, os bytes também têm pesos:

```
Palavra de 32 bits (notação LSB 0):

Bits 31–24  |  Bits 23–16  |  Bits 15–8  |  Bits 7–0
  Byte 3    |    Byte 2    |   Byte 1    |   Byte 0
   MSB      ←——————————————————————————→   LSB
```

| Termo | Sigla | Definição |
|---|---|---|
| **Byte mais significativo** | **MSB** (maiúsculo) | Byte de maior peso — posição mais à esquerda |
| **Byte menos significativo** | **LSB** (maiúsculo) | Byte de menor peso — posição mais à direita |

> [!warning] Não confunda com msb/LSB de bits
> **msb/lsb** (minúsculos) = bit mais/menos significativo — ver [[Dados Binários#MSB e LSB]].
> **MSB/LSB** (maiúsculos) = **byte** mais/menos significativo.
> Os termos se estendem para lsb/LSB e msb/MSB igualmente.

**Notação LSB 0** (padrão deste livro): byte menos significativo à direita; byte mais significativo à esquerda — mesmo padrão da eletrônica digital.

**Notação MSB 0**: usada em redes TCP/IP — byte menos significativo à esquerda. Não abordada aqui.

> [!note] Cardinal vs. ordinal (bytes)
> Assim como nos bits: contagem cardinal começa no byte **0**; o **primeiro** byte é o byte **0**, o segundo é o byte **1**, etc.

## Ordem de armazenamento de bytes — Endianness

Quando um processador manipula dados maiores que 8 bits (ex: um inteiro de 32 bits = 4 bytes), precisa decidir **em que ordem** esses bytes são gravados na [[Memória]]:

| Notação | Ordem de gravação | Posição do byte mais significativo |
|---|---|---|
| **Big-endian** | MSB → LSB (do mais para o menos significativo) | Endereço mais baixo (início) |
| **Little-endian** | LSB → MSB (do menos para o mais significativo) | Endereço mais baixo (início) |

**Exemplo — valor `0x12345678` de 32 bits em 4 bytes de memória:**

| Endereço | Big-endian | Little-endian |
|---|---|---|
| +0 (início) | `0x12` (Byte 3, MSB) | `0x78` (Byte 0, LSB) |
| +1 | `0x34` (Byte 2) | `0x56` (Byte 1) |
| +2 | `0x56` (Byte 1) | `0x34` (Byte 2) |
| +3 | `0x78` (Byte 0, LSB) | `0x12` (Byte 3, MSB) |

> [!info] Quem usa o quê?
> - **[[Intel x86|Processadores x86]]** (PCs) → **little-endian**
> - **Redes TCP/IP** → **big-endian** (chamado de "network byte order")
> - **ARM** → configurável (bi-endian); geralmente little-endian em modo padrão
> - **IBM/Motorola** → big-endian historicamente

> [!note] Por que importa?
> Ao transferir dados entre sistemas de endianness diferente (ex: PC lendo um pacote de rede), os bytes devem ser reordenados. Ignorar isso causa leitura de valores incorretos sem qualquer erro aparente — um dos bugs mais difíceis de encontrar em sistemas de rede e protocolos.

## Abreviação: bit vs. byte (normas)

| Símbolo | Representa | Norma |
|---|---|---|
| **B** (maiúsculo) | byte | IEEE 1541-2002 |
| **b** (minúsculo) | bit | IEEE 1541-2002 |
| "bit" (por extenso) | bit | IEC 80000-13:2008 (preferida por este livro) |

> [!warning] Erro frequente na mídia
> Fabricantes, sites e publicações frequentemente confundem **b** e **B**. Um erro de capitalização representa uma diferença de **8×**. Exemplos comuns:
> - "100 Mbps" = 100 megabits/s ≠ "100 MBps" = 100 megabytes/s (800 Mbps)
> - Velocidade de download anunciada em Mbps, exibida pelo SO em MB/s → divide por 8

Ver [[Unidades Métricas]] para prefixos (kilo, mega, giga) e a distinção KB vs. kbps.

## Ver também
- [[Dados Binários]] — sistema binário, MSB/LSB como bits, representação posicional
- [[Sistemas de Representação]] — unsigned, signed, ponto flutuante (IEEE 754)
- [[Unidades Métricas]] — KB, MB, GB e prefixos métricos
- [[Memória]] — organização em bytes
- [[Intel x86]] — processadores x86 usam little-endian
