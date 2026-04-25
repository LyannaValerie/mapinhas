---
title: Instruções de String x86
aliases:
  - string instructions x86
  - MOVS
  - CMPS
  - SCAS
  - LODS
  - STOS
  - REP prefix
  - REPE
  - REPNE
tags:
  - computação/arquitetura
date: 2026-04-24
source: "PC Assembly Language — Paul A. Carter (2019)"
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Instruções de String x86

## Definição
Família de instruções x86 projetadas para operar sobre arrays de bytes/words/dwords em memória. Usam ESI (fonte) e EDI (destino) e incrementam/decrementam automaticamente os índices após cada operação. Combináveis com o prefixo `REP` para repetição via ECX.

## Relações (SPO)
- Instruções de string → usam → [[Registradores x86#Registradores de Índice|ESI e EDI]]
- Direction flag (DF) → controla → sentido do incremento de ESI/EDI
- Prefixo REP → repete → instrução usando ECX como contador
- REPE/REPNE → combinam → REP com verificação de ZF
- LODSx → lê → [DS:ESI] para AL/AX/EAX
- STOSx → escreve → AL/AX/EAX para [ES:EDI]
- MOVSx → copia → [DS:ESI] para [ES:EDI]
- CMPSx/SCASx → comparam → memória e setam FLAGS

---

## Direction Flag (DF)

Controla se ESI/EDI são **incrementados** ou **decrementados** após cada instrução de string:

```nasm
CLD    ; Clear Direction Flag → ESI/EDI incrementados (+1/+2/+4)
STD    ; Set Direction Flag   → ESI/EDI decrementados (-1/-2/-4)
```

> [!warning] Erro comum
> Esquecer `CLD` antes de instruções de string. O DF pode estar em estado imprevisível, causando comportamento errático. Sempre inicializar explicitamente.

---

## LODSx — Leitura de Memória

Carrega [DS:ESI] em AL/AX/EAX e ajusta ESI.

| Instrução | Operação | Ajuste de ESI |
|---|---|---|
| `LODSB` | `AL = [DS:ESI]` | ESI ± 1 |
| `LODSW` | `AX = [DS:ESI]` | ESI ± 2 |
| `LODSD` | `EAX = [DS:ESI]` | ESI ± 4 |

---

## STOSx — Escrita em Memória

Armazena AL/AX/EAX em [ES:EDI] e ajusta EDI.

| Instrução | Operação | Ajuste de EDI |
|---|---|---|
| `STOSB` | `[ES:EDI] = AL` | EDI ± 1 |
| `STOSW` | `[ES:EDI] = AX` | EDI ± 2 |
| `STOSD` | `[ES:EDI] = EAX` | EDI ± 4 |

---

## MOVSx — Cópia de Memória

Copia [DS:ESI] para [ES:EDI] e ajusta ambos os índices.

| Instrução | Operação | Ajuste |
|---|---|---|
| `MOVSB` | `byte [ES:EDI] = byte [DS:ESI]` | ESI ± 1, EDI ± 1 |
| `MOVSW` | `word [ES:EDI] = word [DS:ESI]` | ESI ± 2, EDI ± 2 |
| `MOVSD` | `dword [ES:EDI] = dword [DS:ESI]` | ESI ± 4, EDI ± 4 |

`MOVSD` equivale a `LODSD` + `STOSD` combinados, mas sem usar EAX.

---

## CMPSx / SCASx — Comparação

Setam FLAGS como `CMP` mas sem armazenar resultado.

### CMPSx — compara [DS:ESI] com [ES:EDI]

| Instrução | Compara | Ajuste |
|---|---|---|
| `CMPSB` | byte [DS:ESI] vs byte [ES:EDI] | ESI ± 1, EDI ± 1 |
| `CMPSW` | word [DS:ESI] vs word [ES:EDI] | ESI ± 2, EDI ± 2 |
| `CMPSD` | dword [DS:ESI] vs dword [ES:EDI] | ESI ± 4, EDI ± 4 |

### SCASx — busca AL/AX/EAX em [ES:EDI]

| Instrução | Compara | Ajuste |
|---|---|---|
| `SCASB` | AL vs byte [ES:EDI] | EDI ± 1 |
| `SCASW` | AX vs word [ES:EDI] | EDI ± 2 |
| `SCASD` | EAX vs dword [ES:EDI] | EDI ± 4 |

> [!note] EDI após SCASx
> `SCASx` incrementa EDI mesmo quando encontra o valor. Para obter o endereço do elemento encontrado, subtrair o tamanho do elemento de EDI.

---

## Prefixo REP

Repete a instrução de string seguinte **ECX vezes** (decrementa ECX a cada iteração).

```nasm
cld
mov   edi, array
mov   ecx, 10
xor   eax, eax
rep stosd            ; zera 10 dwords em array
```

Equivale ao loop manual mas em uma instrução.

---

## Prefixos REPx (com comparação)

Para uso com CMPSx e SCASx:

| Prefixo | Alias | Condição de parada |
|---|---|---|
| `REPE` | `REPZ` | Para quando ECX = 0 **ou** ZF = 0 (resultado diferente) |
| `REPNE` | `REPNZ` | Para quando ECX = 0 **ou** ZF = 1 (resultado igual) |

```nasm
; busca valor 12 em array de 100 dwords
mov   edi, array
mov   ecx, 100
mov   eax, 12
cld
repne scasd          ; busca até encontrar 12 ou esgotar ECX
jne   not_found      ; ZF=0 → não encontrou
sub   edi, 4         ; ajusta: EDI aponta um elemento além do encontrado
; EDI agora aponta para o elemento 12
```

---

## Exemplos

### Copiar array

```nasm
cld
mov   esi, array1    ; fonte
mov   edi, array2    ; destino
mov   ecx, 10        ; 10 elementos
rep movsd            ; copia 10 dwords
```

### Zerar array

```nasm
cld
mov   edi, array
mov   ecx, 10
xor   eax, eax
rep stosd
```

### Loop manual equivalente a LODSx + STOSx

```nasm
cld
mov   esi, array1
mov   edi, array2
mov   ecx, 10
lp:
    lodsd            ; EAX = [ESI], ESI += 4
    stosd            ; [EDI] = EAX, EDI += 4
    loop lp
```

---

## Ver também
- [[Registradores x86]]
- [[Modos de Endereçamento]]
- [[NASM — Sintaxe e Diretivas]]
- [[Operações Bit a Bit]]
- [[Linguagem Assembly]]
