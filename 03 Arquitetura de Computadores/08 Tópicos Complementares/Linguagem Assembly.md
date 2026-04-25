---
title: Linguagem Assembly
aliases:
  - assembly
  - linguagem de montagem
  - assembler
  - nível 4
tags:
  - computação/fundamentos
date: 2026-04-06
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Linguagem Assembly

## Definição
Nível 4 da [[Níveis de Abstração|organização estruturada de computadores]]. É uma forma **simbólica** das linguagens dos níveis inferiores ([[ISA]], nível 2 ou 3), que substitui sequências numéricas por mnemonics legíveis por humanos. Programas em assembly são traduzidos para o nível 1, 2 ou 3 por um programa chamado **assembler**.

## Relações (SPO)
- Linguagem Assembly → é o nível 4 de → [[Níveis de Abstração]]
- Linguagem Assembly → representa simbolicamente → [[ISA]] (nível 2)
- Linguagem Assembly → traduzida por → assembler
- Linguagem Assembly → mais legível que → [[Linguagem de Máquina]]
- Linguagem Assembly → menos abstrata que → linguagens de alto nível (nível 5)

## Posição na hierarquia

```
Nível 5 → Linguagem de alto nível (C, Python…)   ← compilador
Nível 4 → Linguagem Assembly                      ← assembler  ← AQUI
Nível 3 → Sistema Operacional
Nível 2 → ISA
```

## Características

- Primeira linguagem da hierarquia a usar **palavras e abreviações** (não apenas números)
- Cada instrução assembly corresponde essencialmente a uma instrução de máquina (relação quase 1:1)
- Permite ao programador controlar diretamente [[Registrador|registradores]] e [[Memória]]
- Usada por **programadores de sistemas** (não de aplicações)
- Específica de família de máquinas — não é portável entre arquiteturas diferentes
- Acesso total ao hardware: registradores, bits de status, instruções privilegiadas

> [!tip] Assembly vs. linguagem de máquina
> Assembly é uma representação simbólica legível da [[Linguagem de Máquina]]. O assembler faz a conversão de volta para os números binários que o processador entende.

> [!tip] Assembly vs. linguagem de alto nível
> Assembly expõe todos os recursos da máquina-alvo (ex: bit de overflow). Linguagens de alto nível abstraem esses recursos e não permitem acesso direto. Linguagens de sistemas como C são híbridas: sintaxe de alto nível + acesso próximo à máquina.

## Por que usar assembly?

**Desempenho**: programador especializado produz código menor e mais rápido que compiladores, crítico em:
- Laços internos de aplicações de alto desempenho
- Drivers de dispositivos e rotinas de BIOS
- Código em smart cards, cartões RFID, sistemas embarcados
- Bibliotecas de manipulação de strings

**Acesso à máquina**: alguns recursos são inacessíveis em linguagens de alto nível:
- Tratadores de interrupção e exceção de baixo nível
- Controladores de dispositivos em sistemas embarcados de tempo real

> [!note] Estratégia prática
> Escrever tudo em linguagem de alto nível, medir onde o tempo é gasto, reescrever apenas as partes críticas em assembly. Na prática, pequena fração do código consome grande fração do tempo de execução.

## Formato de uma declaração

Toda declaração assembly tem até quatro campos:

| Campo | Função | Obrigatório? |
|---|---|---|
| **Etiqueta** (label) | Nome simbólico para endereço de memória | Não |
| **Opcode** | Mnemônico da instrução ou pseudoinstrução | Não |
| **Operandos** | Registradores, constantes, endereços | Não |
| **Comentários** | Documentação para humanos | Não |

```asm
; Exemplo MASM x86 — cálculo N = I + J
FORMULA:  MOV  EAX, I    ; registrador EAX = I
          ADD  EAX, J    ; registrador EAX = I + J
          MOV  N, EAX    ; N = I + J
I         DD   3         ; reserva 4 bytes com valor 3
J         DD   4         ; reserva 4 bytes com valor 4
N         DD   0         ; reserva 4 bytes com valor 0
```

- Etiquetas de código (MASM) levam `:` — permite etiqueta sozinha na linha
- Etiquetas de dados (MASM) não levam `:`
- Cada máquina/assembler tem convenção própria — não é ditada pela arquitetura

## Pseudoinstruções (diretivas de assembler)

Comandos para o **assembler** (não para o processador). Não geram instruções de máquina.

| Pseudoinstrução | Função |
|---|---|
| `SEGMENT` / `ENDS` | Delimita segmento (texto, dados etc.) |
| `ALIGN n` | Alinha próximo dado/instrução a múltiplo de n |
| `EQU` | Define símbolo igual a expressão (`BASE EQU 1000`) |
| `DB` / `DW` / `DD` / `DQ` | Aloca 1 / 2 / 4 / 8 bytes inicializados |
| `PROC` / `ENDP` | Delimita procedimento |
| `MACRO` / `ENDM` | Delimita definição de [[Macros Assembly|macro]] |
| `PUBLIC` | Exporta símbolo para outros módulos |
| `EXTERN` | Importa símbolo definido em outro módulo |
| `INCLUDE` | Inclui outro arquivo fonte |
| `IF` / `ELSE` / `ENDIF` | Montagem condicional |
| `END` | Marca fim do programa |

> [!example] Montagem condicional
> ```asm
> WORDSIZE EQU 32
> IF WORDSIZE GT 32
>   WSIZE: DD 64
> ELSE
>   WSIZE: DD 32
> ENDIF
> ```
> Permite manter um único fonte para múltiplas máquinas-alvo; só muda `WORDSIZE`.

## Assembler
Programa que realiza a tradução de assembly para linguagem de máquina (nível 1/2/3). A conversão é direta: cada mnemônico corresponde a **um único código numérico** no conjunto de instruções do processador. Pode ser feita:
- **Manualmente** — o programador consulta a tabela do conjunto de instruções e substitui mnemônicos pelos valores.
- **Automaticamente** — usando um programa assembler.

Internamente, o assembler opera em **duas passagens**. Ver [[Processo de Montagem]].

> [!example] Exemplo x86 concreto
> Instrução assembly: `mov ax, bx`
> Significado: copia o conteúdo do registrador BX para o registrador AX.
> Opcode (código de máquina): **89D8h**
>
> Este é o valor que o processador recebe e executa. O assembler faz exatamente essa substituição mnemônico → número.

## Ver também
- [[ISA]]
- [[Linguagem de Máquina]]
- [[Níveis de Abstração]]
- [[Tradução e Interpretação]]
- [[Registrador]]
- [[Processador]]
- [[Macros Assembly]]
- [[Processo de Montagem]]
- [[Ligador]]
- [[Processador 8088]] — ISA 8088, registradores, modos de endereçamento, assembler as88
- [[NASM — Sintaxe e Diretivas]] — sintaxe NASM: segmentos, diretivas de dados, %define, global/extern
