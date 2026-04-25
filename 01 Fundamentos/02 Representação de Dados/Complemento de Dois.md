---
title: Complemento de Dois
aliases:
  - two's complement
  - complemento a dois
  - representação sinalizada
tags:
  - computação/fundamentos
  - computação/representação-de-dados
date: 2026-04-24
source: "The Art of Assembly Language — Randall Hyde (HLA ed.), Cap. 2"
---

# Complemento de Dois

## Definição
Sistema de representação de **inteiros sinalizados** em hardware. O bit mais significativo (H.O.) é o **bit de sinal**: 0 = positivo, 1 = negativo. Permite usar o mesmo hardware para soma e subtração de valores sinalizados e não-sinalizados.

## Relações (SPO)
- Complemento de Dois → representa → inteiros negativos em binário de tamanho fixo
- Bit H.O. = 1 → número é → negativo
- Bit H.O. = 0 → número é → positivo (formato binário padrão)
- `neg` (x86) → computa → negação em Complemento de Dois
- [[Flags Bit a Bit]] → detecta → overflow em aritmética sinalizada (flag OF)
- [[Operações Bit a Bit]] → implementa → conversão via NOT + 1

## Faixas por tamanho

| Bits | Menor (negativo) | Maior (positivo) | Fórmula |
|------|-----------------|-----------------|---------|
| 8    | −128            | +127            | −2^7 .. +2^7 − 1 |
| 16   | −32.768         | +32.767         | −2^15 .. +2^15 − 1 |
| 32   | −2.147.483.648  | +2.147.483.647  | −2^31 .. +2^31 − 1 |
| n    | −2^(n−1)        | +2^(n−1) − 1    | geral |

## Como converter positivo → negativo

Algoritmo de dois passos:

1. Inverter todos os bits (`NOT`)
2. Somar 1 ao resultado, ignorar carry do H.O.

**Exemplo: −5 em 8 bits**

```
%0000_0101   →  5 original
%1111_1010   →  NOT (inverter todos os bits)
%1111_1011   →  +1  →  resultado: −5
```

Aplicar o mesmo processo sobre `%1111_1011` retorna `%0000_0101` — a operação é auto-inversa.

## Instrução x86: `neg`

```asm
neg( dest );    ; dest = -dest  (equivale a NOT dest; ADD dest, 1)
```

Operandos válidos: registrador ou memória (byte, word, dword).

> [!warning] Contexto é responsabilidade do programador
> O hardware não distingue automáticamente sinalizado de não-sinalizado. `%1100_0000` pode ser 192 (unsigned), −64 (signed) ou um caractere ASCII — o programador define e deve ser consistente.

## Propriedades

- **Mesmo hardware** para add/sub sinalizado e não-sinalizado: carry do H.O. é ignorado e o resultado é correto para ambos
- **Zero tem representação única** (vantagem sobre complemento de um)
- Overflow detecável: se dois positivos somam negativo (ou vice-versa), houve overflow

## Ver também
- [[Dados Binários]]
- [[Operações Bit a Bit]]
- [[Flags Bit a Bit]]
- [[Conversão de Bases]]
- [[Registradores x86]]
- [[Sistemas de Representação]]
