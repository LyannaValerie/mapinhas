---
title: Octal
aliases:
  - base 8
  - sistema octal
  - notação octal
tags:
  - computação/fundamentos
date: 2026-04-24
source: "desconhecida"
---

%% criado pelo Codex %%
%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Fundamentos]]

# Octal

## Definição
Sistema de numeração de **base 8**. Usa os algarismos `0` a `7` e pode representar números binários de forma mais compacta para leitura humana.

## Relações (SPO)
- Octal → cada dígito → equivale a **3 bits**
- Octal → representa → [[Dados Binários]] por agrupamento
- Octal → é menos compacto que → [[Hexadecimal]]
- [[Conversão de Bases]] → inclui → conversão binário-octal

## Representação posicional

$$N_8 = d_n \times 8^n + \ldots + d_1 \times 8^1 + d_0 \times 8^0$$

Exemplo: $375_8 = 3 \times 64 + 7 \times 8 + 5 \times 1 = 192 + 56 + 5 = 253_{10}$

## Conversão com binário

Como `8 = 2³`, cada dígito octal corresponde exatamente a um grupo de 3 bits.

| Binário | Octal |
|---|---:|
| `000` | 0 |
| `001` | 1 |
| `010` | 2 |
| `011` | 3 |
| `100` | 4 |
| `101` | 5 |
| `110` | 6 |
| `111` | 7 |

### Binário → Octal
Agrupar bits de 3 em 3 da direita para a esquerda (preencher com zeros à esquerda se necessário):

```
000 111 101 011 = 0753 (octal)
  0   7   5   3
```

### Octal → Binário
Substituir cada dígito pelos 3 bits equivalentes:
```
7  5  3        (octal)
111 101 011    (binário)
```

## Decimal → Octal (divisões sucessivas)

Dividir por 8 repetidamente; restos lidos de baixo para cima:

```
253 ÷ 8 = 31  resto 5
 31 ÷ 8 =  3  resto 7
  3 ÷ 8 =  0  resto 3
→ 253₁₀ = 375₈
```

## Uso prático: permissões Unix/Linux

```
chmod 755 arquivo
```

| Dígito | Valor | Binário | Significado |
|---|---|---|---|
| 7 | dono | 111 | rwx |
| 5 | grupo | 101 | r-x |
| 5 | outros | 101 | r-x |

Cada dígito octal representa 3 bits de permissão (read/write/execute).

## Comparação com hexadecimal

Octal reduz cadeias binárias longas, mas [[Hexadecimal]] é preferido em computação moderna porque cada dígito hex corresponde a 4 bits, alinhando melhor com bytes e nibbles.

## Ver também
- [[Conversão de Bases]]
- [[Hexadecimal]]
- [[Dados Binários]]
- [[Sistema Decimal]]

