---
title: Flags Bit a Bit
aliases:
  - bit flags
  - máscara de bits
  - bitmask
  - flags binárias
tags:
  - computação/fundamentos
  - computação/arquitetura
date: 2026-04-24
source: "desconhecida"
---

%% criado pelo Codex %%
%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Fundamentos]]

# Flags Bit a Bit

## Definição
Técnica de representação em que cada bit de uma palavra indica uma opção booleana independente. `1` indica opção ligada/verdadeira; `0` indica opção desligada/falsa.

## Relações (SPO)
- Flags Bit a Bit → usam → [[Dados Binários]]
- Flags Bit a Bit → são manipuladas por → [[Operações Bit a Bit]]
- Máscara de bits → seleciona → subconjunto de flags
- [[Flag]] → pode ser implementada como → bit individual

## Modelo

Se uma palavra de 4 bits representa quatro preferências, cada posição precisa ter significado fixo:

| Bit | Significado |
|---:|---|
| 3 | comida |
| 2 | música |
| 1 | roupa formal |
| 0 | futebol |

O padrão `1101` indica bits 3, 2 e 0 ligados, e bit 1 desligado.

## Máscara

Uma máscara isola ou altera bits específicos:

```text
  1101
AND 0001
= 0001
```

Resultado diferente de zero indica que a flag do bit 0 está ligada.

## Exemplos reais no IBM PC

### Byte de status do teclado (INT 16h, fn 02H)

| Bit | Significado |
|---|---|
| 0 | Right Shift pressionado |
| 1 | Left Shift pressionado |
| 2 | Ctrl pressionado |
| 3 | Alt pressionado |
| 4 | ScrollLock ativo |
| 5 | NumLock ativo |
| 6 | CapsLock ativo |
| 7 | Insert ativo |

### Status da porta serial (INT 14h — AH de saída)

| Bit | Significado |
|---|---|
| 0 | Data ready |
| 1 | Overrun error |
| 2 | Parity error |
| 3 | Framing error |
| 4 | Break discovered |
| 5 | Transmission hold register empty |
| 6 | Transmission shift register empty |
| 7 | Time out |

### Palavra de equipamento (INT 11h — AX de saída)

| Bits | Significado |
|---|---|
| 0 | 1 = disco presente |
| 1 | 1 = coprocessador matemático presente |
| 4–5 | Modo de vídeo no boot (`01`=40×25 color, `02`=80×25 color, `03`=80×25 mono) |
| 6–7 | Número de disk drives (se bit 0 = 1) |
| 9–11 | Número de portas RS-232 |
| 14–15 | Número de impressoras |

## Manipulação com [[Operações Bit a Bit]]

```c
uint8_t status = bios_keyboard_status(); // INT 16h fn 02H

// Testar CapsLock (bit 6)
if (status & (1 << 6)) { /* CapsLock ativo */ }

// Setar InsertMode (bit 7)
status |= 0x80;

// Limpar CapsLock (bit 6)
status &= ~(1 << 6);
```

> [!note] Registrador FLAGS do x86
> O registrador FLAGS/EFLAGS também é um bitfield: CF (carry), ZF (zero), SF (sign), OF (overflow), IF (interrupt enable), DF (direction). PUSHF/POPF salvam e restauram o registrador inteiro na pilha.

## Ver também
- [[Operações Bit a Bit]]
- [[BIOS INT 16h]]
- [[BIOS Configuração do Sistema]]
- [[Palavras Binárias]]

