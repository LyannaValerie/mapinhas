---
title: Especulação por Compilador
aliases:
  - compiler speculation
  - especulação estática
  - hardware support compiler speculation
  - poison bit
  - advanced load
  - NaT
  - Not a Thing
  - ALAT
  - ld.s
  - chk.s
  - ld.a
  - ld.c
tags:
  - computação/arquitetura
  - computação/ILP
date: 2026-04-22
source: "P&H Apêndice H §H.5"
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Especulação por Compilador

Suporte de hardware que permite ao compilador mover loads **especulativamente** acima de branches ou stores potencialmente conflitantes, adiando o tratamento de exceções e verificação de aliases para quando o resultado for realmente necessário.

## Relações (SPO)

| Sujeito | Predicado | Objeto |
|---|---|---|
| especulação por compilador | move | loads acima de branches/stores |
| especulação por compilador | difere de | [[Especulação Baseada em Hardware]] (hardware decide) |
| poison bit | propaga | informação de exceção diferida |
| ALAT | rastreia | endereços de advanced loads |
| NaT bit | indica | registrador com valor "especulativo inválido" |
| `ld.s` | é | speculative load da IA-64 |
| `chk.s` | verifica | se NaT está ativo e desvia para recovery |
| `ld.a` | é | advanced load (cria entrada no ALAT) |
| `ld.c` / `chk.a` | verificam | se ALAT entry ainda é válida |

## Problema Motivador

```c
// Compilador quer mover o load acima do branch para esconder latência:
if (condição)
    R8 = MEM[R10]   // load com possível exceção

// Mover acima do branch → se condição=false, load não devia executar
// Sem suporte: load pode gerar fault indevido
```

## Quatro Abordagens de Hardware

### 1. Bookkeeping Simples
Compilador garante que a especulação nunca causará exceção — instrução pode ser movida livremente. Sem hardware extra. Funciona apenas em casos trivialmente seguros.

### 2. Poison Bits
- Cada registrador tem um bit extra ("poison bit")
- Load especulativo que geraria exceção → seta poison bit do destino em vez de fazer fault
- Bit propaga via operações aritméticas (NaTVal)
- Instrução real que usa o registrador com poison=1 → **gera exceção real**
- OS deve salvar/restaurar poison bits em context switch

### 3. Advanced Loads — ALAT (IA-64)

Permite ao compilador mover loads acima de stores potencialmente aliased:

```
ld.a R5 = [R10]    // advanced load: cria entrada no ALAT com (R5, addr=R10)

store [R11] = R6   // se R11 == R10 → invalida entrada do ALAT para addr=R10

ld.c R5 = [R10]    // check-load: se ALAT[R10] ainda válido → não faz nada
                   //             se ALAT[R10] inválido   → recarrega de memória
```

O **ALAT** (Advanced Load Address Table) é uma tabela associativa que armazena pares (registrador destino, endereço carregado). Quando um store é executado, todas as entradas com endereço coincidente são invalidadas. `ld.c` confirma que o load especulativo ainda é válido; se não, recarrega.

Variante `chk.a`: em vez de recarregar, desvia para rotina de fix-up que re-executa o load e todo código dependente.

### 4. Reorder Buffer Especulativo
Instruções são marcadas como especulativas com indicador de quantos branches foram cruzados. Hardware força commit em ordem. Equivalente à [[Especulação Baseada em Hardware]] mas iniciada pelo compilador.

## IA-64: NaT — "Not a Thing"

Implementação IA-64 usa abordagem de poison bits:
- Cada registrador de 64 bits tem 1 bit NaT (65° bit lógico)
- **`ld.s`** (speculative load): se load causaria exceção → seta NaT do destino, não faz fault
- **`chk.s`** (check speculative): se NaT=1 → desvia para código de recovery
- NaT propaga via aritmética: `add R3 = R1, R2` com NaT(R1)=1 → NaT(R3)=1
- Store que recebe operando com NaT=1 → **exceção imediata e irrecuperável**
- OS precisa de instrução especial para salvar/restaurar NaT bits em context switch

```
Itanium NaT word: 64 NaT bits para os 64 registradores GP, salvos/restaurados em uma instrução
```

## Comparação das Abordagens

| Abordagem | Overhead hardware | Overhead código | Caso ideal |
|---|---|---|---|
| Bookkeeping | Nenhum | Análise estática | Casos triviais |
| Poison bits (NaT) | 1 bit/registrador | `chk.s` por uso | Loads movidos acima de branches |
| Advanced load (ALAT) | Tabela associativa | `ld.a` + `ld.c`/`chk.a` | Loads movidos acima de stores |
| Reorder buffer | Buffer de reordenação | Marcadores | Especulação dinâmica híbrida |

## Ver também

- [[IA-64 e EPIC]] — implementação com NaT, ld.s/chk.s, ld.a/ld.c, ALAT
- [[Instruções Predicadas]] — abordagem complementar: elimina branch em vez de cruzá-lo
- [[Especulação Baseada em Hardware]] — especulação dinâmica com ROB (hardware decide)
- [[Escalonamento Global de Código]] — contexto onde especulação de compilador é aplicada
- [[VLIW — Very Long Instruction Word]] — requer especulação de compilador para preencher slots
