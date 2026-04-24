---
title: ECC de Memória
aliases:
  - Error Correcting Code
  - código de correção de erros
  - código de Hamming
  - distância de Hamming
  - bit de paridade
  - ECC
  - memória ECC
  - Hamming code
  - palavra de código
tags:
  - computação/arquitetura
date: 2026-04-12
source: "Organização Estruturada de Computadores — Tanenbaum, Cap. 2"
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# ECC de Memória

## Definição
Técnica que adiciona bits redundantes a cada palavra de [[Memória]] para detectar e/ou corrigir erros de bit causados por ruído elétrico, raios cósmicos ou degradação de hardware.

## Relações (SPO)
- ECC → adiciona → r bits de paridade a m bits de dados (palavra total n = m + r)
- Distância de Hamming → mede → número de posições diferentes entre duas palavras de código
- Código de Hamming → corrige → erros de 1 bit; detecta erros de 2 bits
- Bit de paridade simples → detecta → erros de 1 bit; não corrige
- Memória ECC → usada em → servidores e sistemas críticos

## Conceitos Fundamentais

### Distância de Hamming
Número de posições de bit em que duas palavras de código diferem. Calculada por XOR bit-a-bit + contagem dos bits 1 no resultado.

> [!example]
> `10001001` XOR `10110001` = `00111000` → 3 bits diferentes → distância = 3

### Propriedades de Detecção e Correção

| Objetivo | Distância mínima do código |
|---|---|
| Detectar d erros | d + 1 |
| Corrigir d erros | 2d + 1 |

- **Código de distância 2** (paridade simples): detecta 1 erro, não corrige
- **Código de distância 3** (Hamming): corrige 1 erro, detecta 2 erros
- **Código de distância 5**: corrige 2 erros, detecta 4 erros

## Bit de Paridade (distância 2)
Um bit extra faz o número total de 1s ser par (paridade par) ou ímpar (paridade ímpar). Qualquer inversão de 1 bit muda a paridade → erro detectado. Dois erros simultâneos passam despercebidos.

## Código de Hamming (correção de erro único)

Para m bits de dados e r bits de paridade (n = m + r):

$$\text{Requisito: } (m + r + 1) \leq 2^r$$

### Bits de paridade necessários

| Dados (m) | Paridade (r) | Total (n) | Overhead |
|---|---|---|---|
| 4 | 3 | 7 | 75% |
| 8 | 4 | 12 | 50% |
| 16 | 5 | 21 | 31% |
| 32 | 6 | 38 | 19% |
| 64 | 7 | 71 | 11% |
| 128 | 8 | 136 | 6% |

### Regra de Construção

1. Numerar bits de 1 a n (bit 1 = mais à esquerda / alta ordem)
2. Posições que são **potências de 2** (1, 2, 4, 8, 16...) → bits de paridade
3. Demais posições → bits de dados
4. Bit de paridade p verifica todas as posições cujo número tem o bit p ativo

> [!example] Quais posições o bit de paridade 4 verifica?
> Posições com bit 2² ativo em sua representação binária: 4, 5, 6, 7, 12, 13, 14, 15, 20, 21...
> (porque 4 = 100₂; 5 = 101₂; 6 = 110₂; 7 = 111₂ — todos têm o bit 2 ativo)

### Localização de Erro

Ao ler: verificar todos os r bits de paridade. Se paridade par esperada:
1. Listar os bits de paridade **incorretos**
2. Somar seus índices → resultado = posição do bit com erro
3. Inverter esse bit

> [!example] Bits de paridade 1 e 4 incorretos → erro no bit 1 + 4 = **5**

### Exemplo Completo

Palavra de 16 bits `1111000010101110` codificada em 21 bits (5 de paridade):

```
Posição: 1  2  3  4  5  6  7  8  9 10 11 12 13 14 15 16 17 18 19 20 21
Tipo:    P  P  D  P  D  D  D  P  D  D  D  D  D  D  D  P  D  D  D  D  D
Valor:   0  0  1  0  1  1  1  0  0  0  0  0  1  0  1  1  0  1  1  1  0
```
(P = paridade, D = dados)

Se o bit 5 for invertido por falha: bits de paridade 1 e 4 ficam incorretos → soma = 1 + 4 = 5 → bit 5 está errado.

## Memória ECC na Prática

> [!note] Módulos DIMM ECC
> DIMMs padrão: 8 chips de dados (64 bits por acesso). DIMMs ECC: 9 chips (72 bits — 8 extras para correção de 1 bit por byte). Custo modesto; essencial em servidores onde 1 bit errado pode corromper dados críticos.

A taxa média de erro de um módulo DIMM comum é de ~1 erro a cada 10 anos. Em servidores com GBs de RAM processando continuamente, a probabilidade de erro é muito maior.

## Ver também
- [[Memória]] — onde ECC é aplicado
- [[RAM — Tecnologias e Módulos]] — DIMMs com e sem ECC
- [[RAID]] — Reed-Solomon e XOR para correção de erros em disco (princípio similar)
- [[Endianness]] — outro aspecto de organização de memória