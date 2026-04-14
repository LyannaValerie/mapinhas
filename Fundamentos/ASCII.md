---
title: ASCII
aliases:
  - American Standard Code for Information Interchange
  - codificação de caracteres
  - arquivo de texto
  - arquivo binário
  - texto vs binário
tags:
  - computação/fundamentos
date: 2026-04-06
---

# ASCII

## Definição
Padrão de codificação de caracteres que representa cada caractere textual por um **inteiro único de 8 bits** (1 [[Palavras Binárias|byte]]). Permite que texto humano seja armazenado e transmitido como [[Dados Binários]].

## Relações (SPO)
- ASCII → representa → caracteres de texto como inteiros de 1 byte
- ASCII → codifica → letras, dígitos, pontuação e caracteres de controle
- Arquivo de texto → contém → exclusivamente bytes ASCII
- Arquivo binário → contém → bytes com qualquer valor (não necessariamente ASCII)

---

## Funcionamento

Cada caractere tem um valor inteiro fixo. Exemplos:

| Caractere | Valor decimal | Valor hex |
|---|---|---|
| `#` | 35 | 0x23 |
| `i` | 105 | 0x69 |
| `n` | 110 | 0x6E |
| `t` | 116 | 0x74 |
| `SP` (espaço) | 32 | 0x20 |
| `\n` (nova linha) | 10 | 0x0A |
| `{` | 123 | 0x7B |
| `}` | 125 | 0x7D |
| `0` (dígito zero) | 48 | 0x30 |

> [!example] Arquivo hello.c como sequência de bytes
> A primeira linha `#include <stdio.h>` começa com byte 35 (`#`), seguido de byte 105 (`i`), 110 (`n`), 99 (`c`)… Cada linha termina com byte 10 (`\n`).

---

## Arquivos de texto vs. arquivos binários

| Tipo | Conteúdo | Exemplos |
|---|---|---|
| **Arquivo de texto** | Exclusivamente bytes ASCII | `.c`, `.txt`, `.html`, `.py` |
| **Arquivo binário** | Qualquer sequência de bytes | `.exe`, `.o`, `.jpg`, `.pdf` |

> [!note] Consequência prática
> Arquivos de texto podem ser abertos e lidos em qualquer editor. Arquivos binários parecerão "ruído" em editores de texto — pois seus bytes não correspondem a caracteres legíveis.

---

## Informação é bits + contexto

O exemplo do arquivo ASCII ilustra um princípio fundamental da arquitetura de computadores:

> **Toda informação em um sistema é representada como bits. O que distingue diferentes dados é o contexto em que são interpretados.**

O mesmo byte `01101000` pode representar:
- O inteiro **104**
- O caractere **`h`** (ASCII)
- Parte de um número em [[Sistemas de Representação|ponto flutuante]]
- Uma instrução de [[Linguagem de Máquina]]

O processador ou programa que lê o byte decide — pelo contexto — como interpretá-lo.

> [!warning] Consequência para programadores
> Representações numéricas de computadores **não são idênticas** aos inteiros e reais matemáticos — são aproximações finitas que podem se comportar de formas inesperadas (ex: overflow, erros de ponto flutuante). Ver [[Sistemas de Representação]].

---

## Streams de texto (texto como fluxo de caracteres)

O modelo de E/S da biblioteca padrão de C representa toda entrada e saída como **streams de caracteres**:

> **Um stream de texto é uma sequência de caracteres dividida em linhas; cada linha consiste em zero ou mais caracteres seguidos de um caractere de nova linha (`\n`).**

Independentemente de onde o texto vem (teclado, arquivo, rede), a biblioteca padrão garante que aparece como esse fluxo uniforme. Esse modelo é consistente com a [[Sistema Operacional|abstração de arquivo do SO]] — todo dispositivo de E/S é uma sequência de bytes.

---

## Sequências de escape em C

C representa caracteres especiais (não imprimíveis ou difíceis de digitar) dentro de strings como **sequências de escape** — pares iniciados com `\`:

| Sequência | Caractere | Valor ASCII |
|---|---|---|
| `\n` | Nova linha (*newline*) | 10 / 0x0A |
| `\t` | Tabulação horizontal (*tab*) | 9 / 0x09 |
| `\b` | Retrocesso (*backspace*) | 8 / 0x08 |
| `\"` | Aspas duplas | 34 / 0x22 |
| `\\` | Barra invertida | 92 / 0x5C |

Cada sequência representa **um único byte** — `\n` não são dois caracteres, é um.

> [!note] `printf` não insere `\n` automaticamente
> Diferente de `println` em outras linguagens, `printf` em C nunca adiciona newline sozinho. É responsabilidade do programador incluir `\n` quando necessário.

---

## Limitações do ASCII

ASCII foi projetado para o inglês — 128 caracteres (7 bits, extendido para 8). Idiomas não latinos (árabe, chinês, japonês, português com acentos no conjunto extendido) exigem padrões mais amplos:
- **ISO 8859-1 (Latin-1 / IS 8859)** — estendeu o ASCII para 256 caracteres com acentos latinos
- **Páginas de código IS 8859** — conjuntos de 256 caracteres por idioma/região (IS 8859-2 = eslavo latino, IS 8859-3 = turco/maltês etc.); impossível misturar idiomas em uma página
- **Unicode** — padrão moderno; representa virtualmente todos os sistemas de escrita do mundo

---

## Unicode

Consórcio de fabricantes (IS 10646). Cada caractere/símbolo recebe um **ponto de código** único. Suportado em Java, Windows e maioria dos sistemas modernos.

- Ponto de código original: 16 bits → 65.536 posições
- Pontos 0–255 = Latin-1 → compatibilidade retroativa com ASCII
- Sinais diacríticos têm pontos próprios; software os combina com letras base (economiza pontos)
- Em 1996: 16 planos extras de 16 bits adicionados → total **1.114.112 pontos de código**

### Blocos Unicode (exemplos)

| Bloco | Pontos alocados |
|---|---|
| Latino | 336 |
| Grego | 144 |
| Cirílico | 256 |
| Hebraico | 112 |
| Devanágari | 128 |
| Símbolos matemáticos | 256 |
| Ideogramas Han (chinês/japonês) | 20.992 |
| Sílabas Hangul (coreano) | 11.156 |
| Uso privado | 6.400 |

> [!note] Limitação prática
> Dicionário japonês completo tem ~50.000 kanji. Com apenas 20.992 pontos Han disponíveis, escolhas políticas foram necessárias — processo controverso.

---

## UTF-8

**Unicode Transformation Format — 8 bits.** Codificação de tamanho variável que representa Unicode com eficiência. Dominante na Web.

### Características principais
- Caracteres ASCII (0–127) → **1 byte** idêntico ao ASCII — compatibilidade total
- Caracteres não-ASCII → 2 a 4 bytes
- **Autossincronizável:** bytes de continuação sempre começam com `10`; byte inicial nunca começa com `10` → sempre possível encontrar início do próximo caractere após erro

### Esquema de codificação

| Bits de dados | Byte 1 | Bytes seguintes |
|---|---|---|
| 7 | `0ddddddd` | — |
| 11 | `110ddddd` | `10dddddd` |
| 16 | `1110dddd` | `10dddddd 10dddddd` |
| 21 | `11110ddd` | `10dddddd 10dddddd 10dddddd` |

(d = bits de dados)

### Vantagens sobre Unicode puro (16 bits)
1. Texto ASCII puro: 1 byte/caractere (vs. 2 no Unicode raw)
2. Comprimento do caractere determinado pelo 1º byte
3. Autossincronizável → robusto a erros de transmissão
4. Backward-compatible com ASCII

> [!tip] UTF-8 ⊃ ASCII
> Um arquivo ASCII puro **é** um arquivo UTF-8 válido — cada byte ASCII tem MSB = 0, nunca confundido com byte de continuação.

---

## Ver também
- [[Dados Binários]] — representação binária de toda informação
- [[Palavras Binárias]] — byte como unidade de caractere
- [[Sistemas de Representação]] — outros tipos de interpretação de bytes
- [[Sistema de Compilação]] — código-fonte C é um arquivo de texto ASCII
- [[Endianness]] — ordenação de bytes em palavras multibyte (afeta inteiros, não caracteres ASCII)
