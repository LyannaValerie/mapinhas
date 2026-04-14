---
title: Dados Binários
aliases:
  - binário
  - sistema binário
  - representação binária
  - bit
  - sinal digital
  - digital vs analógico
  - msb
  - lsb
  - bit mais significativo
  - bit menos significativo
tags:
  - computação/fundamentos
date: 2026-04-06
---

# Dados Binários

## Definição
Representação de toda informação processada por um [[Computador]] na forma de sequências de **0s e 1s** (sistema binário). Cada algarismo binário é chamado de **bit** (contração de *binary digit*).

## Relações (SPO)
- Dados Binários → processados por → [[Computador]]
- Dados Binários → compõem → [[Software]]
- Dados Binários → armazenados em → [[Hardware]]
- Sistema digital → substitui → sistema analógico (para processamento de dados)
- Sistema digital → imune a → ruído e atenuação de sinal

## Informação Analógica vs. Digital

### Analógico
Informação que pode assumir **qualquer valor** contínuo — sem valores fixos predefinidos. Exemplos da natureza: cor, som, luminosidade. Na eletrônica: um sinal elétrico analógico pode ser +1 V, +1,35 V, +2,37 V… todos válidos.

**Problema crítico:** se o sinal sofre interferência eletromagnética ou atenuação no caminho, o receptor **não tem como saber** que o valor foi alterado — qualquer valor é aceito como correto.

> [!example] Analogia auditiva
> Uma música gravada em **fita cassete** ou **disco de vinil** (formato analógico) acumula chiados, estalos e som abafado com o tempo. O reprodutor não distingue música de ruído porque ambos estão na faixa de valores aceitáveis.
> O mesmo vale para **rádio AM/FM**: ruídos de transmissão aparecem na reprodução pelo mesmo motivo.

### Digital
Informação com apenas **dois valores possíveis**: `0` e `1`. O nome *digital* vem da analogia com dedos: recolhido = 0, esticado = 1.

**Vantagem fundamental:** o receptor sabe exatamente quais valores são válidos. Qualquer coisa fora dessas faixas é ignorada — o sinal chega **intacto** ao destino.

> [!example] Analogia auditiva (digital)
> Uma música em **CD**, **MP3** ou **streaming** não apresenta chiados ou degradação, mesmo depois de décadas armazenada. Qualquer adulteração no sinal é descartada por estar fora dos valores digitais válidos.
>
> Em casos graves de dano físico (CD riscado, setor corrompido), o pior que ocorre é um **silêncio momentâneo** (dados irrecuperáveis descartados) ou **repetição de um trecho** — nunca chiados ou estalos como no analógico.

## Representação física: tensão elétrica

Na prática, os bits `0` e `1` são transmitidos como níveis de tensão elétrica. Uma codificação comum (TTL):

| Bit | Tensão típica | Faixa de tolerância |
|---|---|---|
| `0` (nível lógico baixo) | 0 V | 0 V a 0,8 V |
| `1` (nível lógico alto) | +5 V | 2 V a 5 V |
| *indefinido* | entre 0,8 V e 2 V | *valor inválido — descartado* |

> [!note] Tolerância
> A faixa de tolerância garante que pequenas variações no sinal (ruído, atenuação) não corrompam a informação — enquanto o valor permanecer dentro da faixa correta, é interpretado como 0 ou 1. Valores fora das duas faixas são inválidos.

## Vantagens do sistema digital

1. **Imunidade intrínseca a ruído** — apenas dois estados válidos; variações pequenas são absorvidas pela tolerância
2. **Simplificação da eletrônica** — circuitos que distinguem apenas dois estados são muito mais simples que os analógicos
3. **Detecção e correção de erros** — mecanismos adicionais permitem garantir matematicamente que dados chegaram íntegros (ver capítulo seguinte)

## Representação posicional binária

O sistema binário segue a mesma lógica posicional do decimal — cada posição tem um **peso** que é uma potência da base.

### Decimal (base 10) — revisão
Cada casa tem peso 10ⁿ (da direita para a esquerda, começando em 10⁰):

| 10³ | 10² | 10¹ | 10⁰ |
|---|---|---|---|
| 1.000 | 100 | 10 | 1 |

Exemplos: `329 = 3·10² + 2·10¹ + 9·10⁰ = 300 + 20 + 9`

### Binário (base 2)
Cada casa tem peso 2ⁿ. Para um número de 8 bits:

| Bit 7 | Bit 6 | Bit 5 | Bit 4 | Bit 3 | Bit 2 | Bit 1 | Bit 0 |
|---|---|---|---|---|---|---|---|
| 2⁷ = 128 | 2⁶ = 64 | 2⁵ = 32 | 2⁴ = 16 | 2³ = 8 | 2² = 4 | 2¹ = 2 | 2⁰ = 1 |

**Exemplos de conversão binário → decimal:**
- `0` = 0·2⁰ = **0**
- `1` = 1·2⁰ = **1**
- `110` = 1·2² + 1·2¹ + 0·2⁰ = 4 + 2 + 0 = **6**
- `10111` = 1·2⁴ + 0·2³ + 1·2² + 1·2¹ + 1·2⁰ = 16 + 0 + 4 + 2 + 1 = **23**

> [!note] Custo de representação
> O número 522 exige apenas 3 dígitos em decimal, mas **10 bits** em binário (`1000001010`). O sistema binário é menos eficiente em espaço que o decimal — mas muito mais simples de implementar em eletrônica.

## MSB e LSB — bit mais e menos significativo

Os bits são numerados **da direita para a esquerda**, começando em zero (notação **lsb 0** — padrão deste livro e da eletrônica digital):

```
Bit 7 | Bit 6 | Bit 5 | Bit 4 | Bit 3 | Bit 2 | Bit 1 | Bit 0
 MSB  ←————————————————————————————————————————————————→  LSB
```

| Termo | Sigla | Definição |
|---|---|---|
| **bit mais significativo** | **msb** (minúsculo) | Bit de maior peso — extrema esquerda |
| **bit menos significativo** | **lsb** (minúsculo) | Bit de menor peso — extrema direita (peso 2⁰) |

> [!warning] Maiúsculas vs. minúsculas
> **msb/lsb** (minúsculos) = bit mais/menos significativo.
> **MSB/LSB** (maiúsculos) = **Byte** mais/menos significativo — ver [[Palavras Binárias]].

**Notação alternativa — msb 0:** usada em redes TCP/IP — numera bits da esquerda para a direita (o bit da esquerda é o 0, mais significativo). Não é abordada aqui.

> [!note] Cardinal vs. ordinal
> Contagem cardinal (zero, um, dois…): começa no bit **0**.
> Contagem ordinal (primeiro, segundo…): o **primeiro** bit é o bit **0**, o segundo bit é o bit **1**, e assim por diante.

## Informação é bits + contexto

> **Toda informação em um sistema é representada como bits. O que distingue diferentes dados é o contexto em que são interpretados.**

O mesmo byte pode representar, dependendo do contexto:
- Um **inteiro** (ex: valor 104)
- Um **caractere de texto** (ex: `h` em [[ASCII]])
- Parte de um número em **ponto flutuante** ([[Sistemas de Representação]])
- Uma **instrução de máquina** ([[Linguagem de Máquina]])

Isso inclui tudo no sistema: arquivos em disco, programas na memória, dados de usuário e dados transmitidos pela rede — todos são apenas bits. O programa ou processador que acessa esses bits decide, pelo contexto, como interpretá-los.

## Contexto no computador
Para o [[Computador]], qualquer dado — texto, imagem, vídeo, áudio, instruções de programa — é internamente apenas uma sequência de bits organizados em [[Palavras Binárias]]. A tradução desses bits em algo significativo para o usuário é feita pelas camadas de [[Hardware]] e [[Software]].

## Ver também
- [[Computador]]
- [[Hardware]]
- [[Software]]
- [[Linguagem de Máquina]]
- [[Palavras Binárias]] — nibble, byte, word, dword, qword; MSB/LSB como bytes
- [[Hexadecimal]] — representação compacta de binário; notações de base ($, 0x); padding; overflow
- [[Unidades Métricas]] — como KB/MB/GB se relacionam com o sistema binário
