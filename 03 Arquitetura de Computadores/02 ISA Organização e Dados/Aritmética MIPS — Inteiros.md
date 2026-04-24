---
title: Aritmética MIPS — Inteiros
aliases:
  - MIPS integer arithmetic
  - mult MIPS
  - div MIPS
  - Hi Lo registers MIPS
  - overflow MIPS exception
  - restoring division
  - SRT division
  - algoritmo de multiplicação
  - algoritmo de divisão
tags:
  - computação/arquitetura
  - computação/MIPS
date: 2026-04-23
source: "Patterson & Hennessy — Computer Organization and Design, Cap. 3"
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Aritmética MIPS — Inteiros

Complemento a [[Aritmética Inteira]] e [[Sistemas de Representação]] com o ponto de vista da ISA e hardware MIPS.

## Relações (SPO)

- add/sub MIPS → dispara → exceção em overflow (complemento de dois)
- addu/subu MIPS → ignora → overflow (uso padrão em C)
- mult/multu → produz → produto de 64 bits em Hi:Lo
- div/divu → produz → quociente em Lo, resto em Hi
- EPC → armazena → endereço da instrução que causou exceção
- mfc0 → copia → EPC para registrador de propósito geral
- $k0/$k1 → reservados para → OS (não restaurados em exceções)
- SRT division → produz → até 4 bits de quociente por passo

---

## 3.2 — Atalhos com Complemento de Dois

### Negação

Inverter todos os bits + 1.

$$-x = \overline{x} + 1$$

Verificação: $x + \overline{x} = 111\ldots1_2 = -1$, então $x + \overline{x} + 1 = 0$, logo $\overline{x} + 1 = -x$.

### Extensão de Sinal (Sign Extension)

Para ampliar de $n$ para $m$ bits ($m > n$): replicar o bit de sinal $m - n$ vezes à esquerda.

```
16 bits: 1111 1111 1111 1110  = –2
32 bits: 1111 1111 1111 1111  1111 1111 1111 1110  = –2
```

MIPS usa extensão de sinal no campo imediato de 16 bits para operações de 32 bits (lw, addi, beq, etc.).

### Atalho de Verificação de Limites de Array

Para checar `0 ≤ k < n` com uma única comparação:

```mips
sltu $t0, $a1, $t2   # $t0 = 0 se k ≥ n OU k < 0 (negativo vira grande em unsigned)
beq  $t0, $zero, IndexOutOfBounds
```

Negativo em complemento de dois → número grande em unsigned → sltu detecta ambos os casos.

---

## 3.3 — Adição, Subtração e Overflow

### Instruções

| Instrução | Overflow? | Uso típico |
|---|---|---|
| `add`, `addi`, `sub` | Sim (exceção) | Fortran; raramente em C |
| `addu`, `addiu`, `subu` | Não | C (compiladores sempre geram unsigned) |

### Condições de Overflow na Adição

| Operação | Sinal de A | Sinal de B | Resultado indica overflow |
|---|---|---|---|
| A + B | ≥ 0 | ≥ 0 | < 0 |
| A + B | < 0 | < 0 | ≥ 0 |
| A − B | ≥ 0 | < 0 | < 0 |
| A − B | < 0 | ≥ 0 | ≥ 0 |

Overflow de sinais diferentes em adição **nunca** ocorre.

### Tratamento de Exceção em MIPS

Overflow detectado → exceção disparada:

1. Endereço da instrução causadora → salvo em **EPC** (Exception Program Counter)
2. CPU salta para endereço predefinido do handler do OS
3. Handler usa **mfc0** para copiar EPC a um registrador geral
4. Retorno via **jump register** ($k0 ou $k1)

```mips
# Detecção manual de overflow em adição signed sem usar add:
addu $t0, $t1, $t2        # soma sem exceção
xor  $t3, $t1, $t2        # bits diferentes nos sinais?
slt  $t3, $t3, $zero      # $t3 = 1 se sinais diferentes
bne  $t3, $zero, No_overflow
xor  $t3, $t0, $t1        # sinais iguais: soma tem sinal diferente?
slt  $t3, $t3, $zero
bne  $t3, $zero, Overflow
```

> [!note] $k0 e $k1
> Registradores reservados para o OS. Compiladores nunca os usam — garantem que o handler possa retornar sem destruir estado do programa.

---

## 3.4 — Multiplicação

### Instrução e Registradores

```mips
mult  $s2, $s3    # Hi:Lo = $s2 × $s3  (signed, 64-bit result)
multu $s2, $s3    # Hi:Lo = $s2 × $s3  (unsigned, 64-bit result)
mflo  $s1         # $s1 = Lo (32 bits inferiores do produto)
mfhi  $s1         # $s1 = Hi (32 bits superiores do produto)
```

Sem detecção de overflow — responsabilidade do software checar Hi:
- Para `multu`: sem overflow se Hi = 0
- Para `mult`: sem overflow se Hi = extensão de sinal de Lo

### Algoritmo Sequencial (Versão 1)

Hardware: Multiplicando (64 bits), Multiplicador (32 bits), Produto (64 bits, init = 0).

```
Para cada um dos 32 bits do Multiplicador (direita → esquerda):
  1. Se bit[0] do Multiplicador = 1: Produto += Multiplicando
  2. Multiplicando <<= 1 (shift esquerda 1 bit)
  3. Multiplicador >>= 1 (shift direita 1 bit)
Repetir 32 vezes → ~100 ciclos no pior caso
```

### Algoritmo Refinado (Versão Final)

Produto (64 bits): metade direita inicializada com o multiplicador.

```
Para cada um dos 32 bits:
  1. Se bit[0] da metade direita de Produto = 1: metade esquerda += Multiplicando
  2. Produto >>= 1 (shift direita, preserva sinal)
Repetir 32 vezes
```

Resultado: Produto[63:32] = Hi, Produto[31:0] = Lo.

### Multiplicação Rápida

Unrola o loop: 32 somadores em paralelo, um por bit do multiplicador.

$$P_{63\ldots0} = \sum_{i=0}^{31} (\text{Mplier}_i \cdot \text{Mcand}) \ll i$$

Somadores com carry save permitem pipeline — suporta múltiplas multiplicações simultâneas.

### Multiplicação por Potência de 2

Shift esquerdo: `sll $t0, $s1, k` equivale a `$s1 × 2^k`.

Compiladores substituem `× constante` por sequências de shift + add.

---

## 3.5 — Divisão

### Instrução e Registradores

```mips
div   $s2, $s3    # Lo = $s2 / $s3 (quociente, signed)
                  # Hi = $s2 mod $s3 (resto, signed)
divu  $s2, $s3    # Lo = $s2 / $s3 (unsigned)
                  # Hi = $s2 mod $s3 (unsigned)
mflo  $s1         # $s1 = quociente
mfhi  $s1         # $s1 = resto
```

Sem exceção automática — software deve verificar:
- Divisor = 0 → dividir por zero
- Overflow (divide –2³¹ por –1 → resultado não cabe em 32 bits)

### Algoritmo Restaurativo (Versão 1)

Hardware: Divisor (64 bits, alinhado à esquerda), Quociente (32 bits), Resto (64 bits, init = dividendo).

```
Para 33 iterações:
  1. Resto = Resto − Divisor
  2a. Se Resto ≥ 0: Quociente <<= 1, bit[0] = 1
  2b. Se Resto < 0: Resto += Divisor (restaura), Quociente <<= 1, bit[0] = 0
  3. Divisor >>= 1 (shift direita 1 bit)
```

> [!note] n+1 iterações
> Algoritmo necessita 33 passos (não 32) para produzir quociente e resto corretos.

### Algoritmo Refinado

ALU de 32 bits (não 64); Resto (64 bits) se desdobra: metade esquerda acumula resto, metade direita acumula quociente. Shift esquerdo no Resto + shift esquerdo no Quociente juntos.

### Divisão Rápida — SRT

Não restaurativa guiada por tabela lookup:
- Usa 6 bits do Resto + 4 bits do Divisor → tabela determina palpite para dígito do quociente (tipicamente 4 bits/passo)
- Passos subsequentes corrigem palpites errados
- Tabela incorreta → resultados errados (bug histórico no Pentium FDIV, 1994)

### Sinal no Quociente e Resto

Regra: sinal do resto = sinal do dividendo (independe do divisor).

$$\text{Dividendo} = \text{Quociente} \times \text{Divisor} + \text{Resto}$$

| Dividendo | Divisor | Quociente | Resto |
|---|---|---|---|
| +7 | +2 | +3 | +1 |
| −7 | +2 | −3 | −1 |
| +7 | −2 | −3 | +1 |
| −7 | −2 | +3 | −1 |

---

## Ver também

- [[Sistemas de Representação]] — complemento de dois, IEEE 754, extensão de sinal
- [[Aritmética Inteira]] — overflow, shift, propriedades algébricas (perspectiva CS:APP)
- [[MIPS — Conjunto de Instruções]] — ISA completa
- [[MIPS — Formato de Instrução]] — formato R, I, J
- [[ULA]] — hardware executor das operações
- [[Ponto Flutuante — Operações]] — operações FP: add, mul, arredondamento
