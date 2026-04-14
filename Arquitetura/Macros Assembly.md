---
title: Macros Assembly
aliases:
  - macro
  - macros
  - expansão de macro
  - processador de macros
tags:
  - computação/fundamentos
date: 2026-04-13
---

# Macros Assembly

## Definição
Mecanismo que associa um **nome simbólico** a um bloco de texto (sequência de instruções). Ao usar o nome no código, o assembler substitui automaticamente pelo bloco correspondente. Solução eficiente para reutilização de sequências de instruções sem o custo de chamadas de procedimento.

## Relações (SPO)
- Macro → é processada por → [[Processo de Montagem|assembler]] em tempo de montagem
- Macro → diferente de → procedimento (executado em tempo de execução)
- Macro → pode ter → parâmetros formais
- Macro → expande para → cópia inline do corpo no programa-objeto
- Processador de macros → manipula → cadeias de caracteres sem considerar semântica

## Definição, chamada e expansão

Três fases do ciclo de vida de uma macro:

1. **Definição** — o programador declara a macro com `MACRO` / `ENDM`
2. **Chamada** — o nome da macro é usado como opcode no código
3. **Expansão** — o assembler substitui o nome pelo corpo durante a **passagem 1** da montagem

```asm
; Definição
SWAP MACRO
  MOV EAX, P
  MOV EBX, Q
  MOV Q, EAX
  MOV P, EBX
ENDM

; Chamadas (cada uma gera cópia completa do corpo)
SWAP
SWAP
```

> [!warning] Expansão ocorre em tempo de montagem, não de execução
> O programa-objeto gerado é idêntico ao que seria escrito sem macros. Não há vestígio das macros no código executável final.

## Macro vs. procedimento

| Item | Macro | Procedimento |
|---|---|---|
| Quando a chamada é processada | Montagem | Execução |
| Corpo inserido inline em cada chamada? | Sim | Não |
| Instrução de chamada no programa-objeto? | Não | Sim |
| Instrução de retorno necessária? | Não | Sim |
| Cópias do corpo no programa-objeto | Uma por chamada | Uma |
| Custo em tempo de execução | Zero (overhead) | Instrução CALL + RET |
| Custo em tamanho de código | Cresce com cada chamada | Fixo |

## Macros com parâmetros

Parâmetros formais permitem reutilizar a macro com operandos diferentes:

```asm
; Definição com parâmetros P1 e P2
CHANGE MACRO P1, P2
  MOV EAX, P1
  MOV EBX, P2
  MOV P2, EAX
  MOV P1, EBX
ENDM

; Chamadas com parâmetros reais
CHANGE P, Q    ; troca P e Q
CHANGE R, S    ; troca R e S
```

Na expansão, cada ocorrência do parâmetro formal é substituída pelo parâmetro real correspondente.

## Características avançadas (MASM)

**Rótulos locais**: macros com desvios condicionais geram rótulos duplicados se chamadas múltiplas vezes. Solução: declarar rótulo como `LOCAL` — o assembler gera rótulo único a cada expansão.

**Macros aninhadas**: macro pode definir outra macro internamente. Combinado com montagem condicional, permite definir comportamentos diferentes dependendo da máquina-alvo:

```asm
M1 MACRO
  IF WORDSIZE GT 16
    M2 MACRO
      ...  ; versão 32+ bits
    ENDM
  ELSE
    M2 MACRO
      ...  ; versão 16 bits
    ENDM
  ENDIF
ENDM
```

**Recursão**: macro pode chamar a si mesma. Deve ter parâmetro de controle que encerra a recursão — caso contrário, o assembler entra em loop infinito.

## Implementação interna

O assembler mantém uma **tabela de definição de macros**. Ao encontrar `MACRO`:
1. Cria entrada na tabela com nome, número de parâmetros, ponteiro para o corpo
2. Armazena o corpo como cadeia de caracteres com marcadores nos parâmetros formais (`&P1`, `&P2`)

Exemplo de representação interna de `CHANGE`:
```
MOV EAX,&P1; MOV EBX,&P2; MOV &P2,EAX; MOV &P1,EBX;
```

Na chamada, o assembler lê o corpo armazenado substituindo `&Pn` pelos parâmetros reais fornecidos.

## Ver também
- [[Linguagem Assembly]]
- [[Processo de Montagem]]
- [[Tradução e Interpretação]]
