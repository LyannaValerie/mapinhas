---
title: VLIW
aliases:
  - Very Long Instruction Word
  - palavra de instrução muito longa
  - EPIC
  - paralelismo explícito no nível da instrução
  - TriMedia
tags:
  - computação/arquitetura
date: 2026-04-21
---

# VLIW — Very Long Instruction Word

## Definição

Arquitetura de CPU onde cada instrução contém **múltiplos opcodes e operandos** — um por unidade funcional disponível. O paralelismo é **explicitado pelo compilador** (não detectado em hardware), ao contrário do [[Concorrência e Paralelismo#Paralelismo a nível de instrução|superescalar]].

## Relações (SPO)
- VLIW → delega a → compilador a detecção de paralelismo entre instruções
- Instrução VLIW → contém → um opcode/par de operandos por unidade funcional
- Compilador → insere → NOP em slots sem operação disponível
- EPIC → é → extensão do VLIW com verificações de dependência em hardware
- TriMedia → é → VLIW da Philips para processamento multimídia embarcado
- Itanium-2 → implementa → EPIC (variante VLIW para uso geral)

---

## Funcionamento

Máquina com 5 unidades funcionais (2 inteiras, 1 FP, 1 LOAD, 1 STORE):

```
┌──────┬──────┬──────┬──────┬──────┐
│ INT₁ │ INT₂ │  FP  │ LOAD │STORE │
│op,R,R│op,R,R│op,R,R│op,R,R│op,R,R│
└──────┴──────┴──────┴──────┴──────┘
       uma instrução VLIW
```

- Com 6 bits/opcode e 5 bits/registrador: cada campo = 16 bits → instrução total = **80 bits**
- Se não há operação para uma unidade no ciclo: slot recebe **NOP** (bits zero)

---

## Comparação com Superescalar

| Aspecto | Superescalar | VLIW |
|---|---|---|
| Detecção de paralelismo | Hardware (run-time) | Compilador (compile-time) |
| Complexidade de hardware | Alta | Baixa |
| Compatibilidade binária | Sim | Problemática |
| Código para máquinas futuras | Recompila automaticamente | Requer recompilação |
| Código com poucos paralelos | Funciona (reduz emissão) | Muitos NOPs → código grande |

---

## Problemas Práticos

1. **Código inchado**: slots NOP ocupam espaço mesmo sem operação útil
2. **Compatibilidade binária**: binário compilado para N unidades não roda em N+2 unidades sem recompilação
3. **Referências antecipadas e latência de memória**: compilador precisa conhecer latências exatas — impossível em presença de cache

---

## Exemplo: CPU TriMedia (Philips)

VLIW projetado para decodificadores de TV digital, câmeras digitais e outros dispositivos multimídia.

| Parâmetro | Valor |
|---|---|
| Operações por ciclo | 5 |
| Unidades funcionais | 27 (distintas) |
| Banco de registradores | 128 registradores de 32 bits |
| Instruções de multimídia | Aceleração nativa de áudio/vídeo |
| Uso típico | Decodificador MPEG, câmera digital |

- Alvo: dispositivos embarcados onde compilador pode ser executado em design-time e compatibilidade binária tem menos importância
- Contrasta com [[IA-64 e EPIC|Itanium-2]] (EPIC para uso geral em servidores)

---

## EPIC — Explicitly Parallel Instruction Computing

Variante VLIW usada no Itanium. Diferença principal: hardware verifica algumas dependências que o compilador não consegue resolver estaticamente (ex: aliasing de ponteiros). Compilador ainda faz a maior parte do trabalho.

> [!note]
> A transição do mercado de VLIW/EPIC para uso geral é difícil — demonstrada pelo insucesso comercial do Itanium fora de nichos específicos. VLIW mantém papel dominante em DSPs e processadores embarcados.

---

## Ver também
- [[IA-64 e EPIC]] — Itanium, EPIC, execução especulativa
- [[Concorrência e Paralelismo]] — paralelismo no nível da instrução, superescalar
- [[Pipeline]] — base do ILP; VLIW como alternativa ao pipeline superescalar
- [[Multithreading no Chip]] — outra técnica de paralelismo no chip
