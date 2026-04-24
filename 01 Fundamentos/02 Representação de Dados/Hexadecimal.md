---
title: Hexadecimal
aliases:
  - base hexadecimal
  - base 16
  - hex
  - 0x
  - notação de base
  - padding
  - preenchimento
  - zeros à esquerda
  - overflow
tags:
  - computação/fundamentos
date: 2026-04-06
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Fundamentos]]

# Hexadecimal

## Definição
Sistema de numeração de **base 16** usado como representação compacta e legível de números binários. É uma notação **exclusivamente para humanos** — componentes eletrônicos digitais continuam operando apenas em binário internamente.

## Relações (SPO)
- Hexadecimal → representa → números binários de forma compacta
- Hexadecimal → cada dígito → equivale a **4 bits** (1 nibble)
- Hexadecimal → adotado por → programadores para evitar erros com longas cadeias de 0s e 1s

---

## Os 16 algarismos

| Decimal | Binário | Hexadecimal |
|---|---|---|
| 0 | 0000 | **0** |
| 1 | 0001 | **1** |
| 2 | 0010 | **2** |
| 3 | 0011 | **3** |
| 4 | 0100 | **4** |
| 5 | 0101 | **5** |
| 6 | 0110 | **6** |
| 7 | 0111 | **7** |
| 8 | 1000 | **8** |
| 9 | 1001 | **9** |
| 10 | 1010 | **A** |
| 11 | 1011 | **B** |
| 12 | 1100 | **C** |
| 13 | 1101 | **D** |
| 14 | 1110 | **E** |
| 15 | 1111 | **F** |

> [!note] Maiúsculas e minúsculas
> A-F e a-f são equivalentes — não há diferença de valor. A maioria dos contextos técnicos usa maiúsculas.

---

## Por que hexadecimal?

Cada dígito hexadecimal representa exatamente **4 bits** (1 [[Palavras Binárias|nibble]]). Isso torna a conversão binário ↔ hex trivial e elimina a necessidade de escrever longas cadeias de 0s e 1s:

| Binário (32 bits) | Hexadecimal (8 dígitos) |
|---|---|
| `00010010001110100001001000111010` | `123A123A` |

Trabalhar com 8 dígitos variados é incomparavelmente mais fácil (e menos sujeito a erros) do que 32 dígitos binários.

**Cálculo de bits a partir de dígitos hex:**
- 1 dígito hex = 4 bits
- `F12AC` (5 dígitos) = **20 bits**
- `129D3E12` (8 dígitos) = **32 bits**

---

## Notações de base numérica

O mesmo número `10` tem valores completamente diferentes dependendo da base:
- `(10)₁₀` = dez (decimal)
- `(10)₂` = dois (binário)
- `(10)₁₆` = dezesseis (hexadecimal)

### Notação matemática
Número entre parênteses, base em subscrito: `(10)₁₀`, `(10)₂`, `(10)₁₆`

### Notações em sistemas computacionais

| Base | Notações comuns | Exemplos |
|---|---|---|
| **Binário** | Prefixo `$` ou sufixo `b` | `$1011`, `1011b` |
| **Hexadecimal** | Sufixo `h` ou prefixo `0x` | `12h`, `0x12` |
| **Decimal** | (sem marcador) | `42` |

> [!warning] Conflito: `b` para binário vs. `b` para bit
> A letra `b` sozinha significa **bit** (IEEE 1541-2002) — não binário. O uso de `b` como sufixo de base binária (`1011b`) é informal e deve ser lido no contexto. Para evitar ambiguidade, prefira `$` ou `0b` como indicador de binário.

---

## Zeros à esquerda e Padding

Zeros à esquerda **não alteram o valor**: `5 = 05 = 00005`. O mesmo vale em binário e hexadecimal:
- `1010b` = `00001010b` (ambos = 10 em decimal)
- `0x1AF` = `0x01AF` (ambos = 431 em decimal)

### Padding (preenchimento)

Quando um valor precisa ser armazenado em um espaço fixo (ex: [[Registrador]] de 32 bits), completa-se com zeros à esquerda até atingir o tamanho necessário:

> **Exemplo:** armazenar `0x1AF` (12 bits = 3 dígitos hex) em registrador de 32 bits (8 dígitos hex):
> → `0x000001AF`

O preenchimento garante que o valor ocupe exatamente o espaço disponível sem alterar seu significado.

---

## Overflow

**Overflow** ocorre quando o valor a ser armazenado ou transportado é **maior do que o espaço disponível** — o valor "não cabe".

> **Exemplo:** valor de 18 bits em espaço de 16 bits → os 2 bits mais significativos não têm onde ir.

### Tratamentos possíveis

| Estratégia | Quando usada |
|---|---|
| **Erro de overflow** | [[Processador]] ao tentar armazenar em [[Registrador]] com menos bits |
| **Armazenamento em posição seguinte** | Dados maiores que a [[Palavras Binárias|word]] do sistema; overflow guardado no endereço de memória adjacente |
| **Múltiplas transferências** | Dado de 128 bits em barramento de 32 bits → 4 transferências sequenciais |
| **Descarte dos bits excedentes** | Algoritmos de soma de verificação (checksum) — bits além do espaço são ignorados ou somados de volta ao valor |

> [!note] Endianness e overflow
> Ao transferir dados em múltiplas partes (ex: 128 bits em 4 × 32 bits), a ordem das partes segue a convenção [[Palavras Binárias#Ordem de armazenamento de bytes — Endianness|big-endian ou little-endian]] do sistema.

---

## Ver também
- [[Dados Binários]] — sistema binário, bits, representação posicional
- [[Palavras Binárias]] — nibble, byte, word; endianness
- [[Sistemas de Representação]] — unsigned, signed, ponto flutuante
- [[Registrador]] — destino de armazenamento; tamanho determina limite de overflow
