---
title: Tipos de Instrução ISA
aliases:
  - categorias de instrução
  - instrução de movimentação
  - instrução diádica
  - instrução monádica
  - instrução de laço
  - LOOP
  - shift aritmético
  - rotate
  - NOP
  - HALT
  - FENCE
  - barreira de memória
tags:
  - computação/arquitetura
date: 2026-04-18
source: "Organização Estruturada de Computadores — Tanenbaum, Cap. 5"
---

# Tipos de Instrução ISA

Taxonomia das categorias funcionais de instruções no nível ISA. Toda ISA oferece alguma variante de cada categoria; o que varia entre arquiteturas é a quantidade de operandos, os modos de endereçamento permitidos e os formatos de instrução.

## Relações (SPO)

- Instrução ISA → pertence a → categoria funcional (move, aritmética, lógica, shift, comparação, desvio, chamada, laço, E/S, conversão)
- Instrução de movimentação → transfere dados entre → registrador, memória, pilha
- Instrução diádica → combina dois operandos em → um resultado
- Instrução monádica → transforma um operando em → um resultado
- Instrução de comparação → ajusta → [[Flag|PSW/flags]] sem armazenar resultado
- Instrução de laço → decrementa contador e → desvia se não-zero
- Instrução de E/S → transfere dados entre → CPU e dispositivo periférico

---

## §5.5.1 — Instruções de Movimentação

Transferem dados sem transformação. Representam boa fração do tráfego de execução.

| Instrução | Operação |
|---|---|
| `MOV dst, src` | copia src → dst (reg→reg, imm→reg, mem→reg, reg→mem) |
| `PUSH src` | SP ← SP − word; mem[SP] ← src |
| `POP dst` | dst ← mem[SP]; SP ← SP + word |
| `XCHG a, b` | troca atômica de dois operandos |
| `MOVS` (x86) | copia bloco de bytes: mem[DI] ← mem[SI]; SI++; DI++ |
| `LEA dst, mem` | carrega endereço efetivo (não o conteúdo) em dst |

> [!note] Distinção MOV vs. LEA
> `MOV EAX, [EBX+4]` carrega o **valor** em memória; `LEA EAX, [EBX+4]` carrega o **endereço** EBX+4. LEA é frequentemente usada para aritmética de endereços.

---

## §5.5.2 — Operações Diádicas (dois operandos)

Recebem dois operandos, produzem um resultado. Subdividem-se em aritméticas e booleanas.

### Aritméticas

| Instrução | Operação |
|---|---|
| `ADD dst, src` | dst ← dst + src |
| `SUB dst, src` | dst ← dst − src |
| `MUL src` | (AL/AX/EAX) ← (AL/AX/EAX) × src — resultado pode ser duplo-largura |
| `DIV src` | quociente + resto em registradores especificados |
| `IMUL / IDIV` | versões com sinal |

### Booleanas (bitwise)

| Instrução | Operação |
|---|---|
| `AND dst, src` | dst ← dst AND src (preserva bits onde src = 1, zera onde src = 0) |
| `OR dst, src` | dst ← dst OR src (seta bits onde src = 1) |
| `XOR dst, src` | dst ← dst XOR src (inverte bits onde src = 1; XOR reg, reg → zera) |

> [!tip] AND como máscara de bits
> `AND EAX, 0xFF` extrai o byte menos significativo de EAX. `OR EAX, 0x80` seta o bit 7. `XOR EAX, EAX` zera EAX em 1 ciclo (alternativa idiomática a `MOV EAX, 0`).

---

## §5.5.3 — Operações Monádicas (um operando)

Recebem um operando, produzem um resultado.

### Negação e complemento

| Instrução | Operação |
|---|---|
| `NEG dst` | dst ← −dst (complemento de dois) |
| `NOT dst` | dst ← ~dst (complemento de um, bitwise) |

### Deslocamento (shift)

| Instrução | Operação | Preenchimento |
|---|---|---|
| `SHL / SAL dst, n` | desloca esquerda n bits | 0 (bit menos significativo) |
| `SHR dst, n` | desloca direita n bits (sem sinal) | 0 (bit mais significativo) |
| `SAR dst, n` | desloca direita n bits (com sinal) | bit de sinal (preserva sinal) |

> [!note] Shift aritmético direito
> `SAR` divide por 2ⁿ (arredondando para −∞). Equivalente a divisão inteira com sinal. `SHR` divide por 2ⁿ mas só funciona corretamente para números sem sinal.

### Rotação

| Instrução | Operação |
|---|---|
| `ROL dst, n` | rotaciona esquerda n bits (bit que sai entra pelo direito) |
| `ROR dst, n` | rotaciona direita n bits |
| `RCL dst, n` | rotaciona esquerda passando pelo carry flag |
| `RCR dst, n` | rotaciona direita passando pelo carry flag |

---

## §5.5.4 — Comparações e Desvios Condicionais

Instruções de comparação ajustam flags sem armazenar resultado; instruções de desvio testam flags para tomar decisão de fluxo.

| Instrução | Operação | Flags ajustadas |
|---|---|---|
| `CMP a, b` | a − b (descarta resultado) | ZF, SF, CF, OF |
| `TEST a, b` | a AND b (descarta resultado) | ZF, SF, PF |

Após `CMP` ou `TEST`, instruções de desvio condicional leem os flags:

| Instrução | Condição |
|---|---|
| `JE / JZ` | ZF = 1 (igual / zero) |
| `JNE / JNZ` | ZF = 0 (diferente / não-zero) |
| `JL / JNGE` | SF ≠ OF (menor — com sinal) |
| `JG / JNLE` | ZF = 0 AND SF = OF (maior — com sinal) |
| `JB / JNAE` | CF = 1 (abaixo — sem sinal) |
| `JA / JNBE` | CF = 0 AND ZF = 0 (acima — sem sinal) |
| `JMP` | desvio incondicional |

Ver [[Flag]] para definição completa dos flags e [[Previsão de Desvio]] para mecanismos de hardware.

---

## §5.5.5 — Chamadas de Procedimento

Instruções CALL/RET transferem controle entre procedimentos com preservação automática do endereço de retorno via pilha.

| Instrução | Operação |
|---|---|
| `CALL addr` | PUSH RIP; JMP addr |
| `RET` | POP RIP |
| `RET n` | POP RIP; SP ← SP + n (limpeza de parâmetros pelo procedimento chamado) |

Ver [[Pilha]] para estrutura do quadro de pilha (stack frame), prólogo/epílogo, recursão e corotinas.

---

## §5.5.6 — Controle de Laço

Instrução especializada que combina decremento de contador e desvio condicional em uma única operação.

| Instrução | Operação |
|---|---|
| `LOOP label` | CX ← CX − 1; se CX ≠ 0, desvia para label |
| `LOOPE label` | CX ← CX − 1; desvia se CX ≠ 0 AND ZF = 1 |
| `LOOPNE label` | CX ← CX − 1; desvia se CX ≠ 0 AND ZF = 0 |

> [!note] LOOP vs. DEC+JNZ
> `LOOP` era vantajoso em CPUs antigas (1 instrução vs. 2). Em CPUs modernas com decodificação superescalar, `DEC ECX; JNZ label` frequentemente tem desempenho equivalente ou superior. Compiladores modernos raramente emitem `LOOP`.

---

## §5.5.7 — Instruções de E/S

Três abordagens para transferência entre CPU e periféricos:

| Abordagem | Instrução ISA | Quem faz a transferência |
|---|---|---|
| E/S programada | `IN`/`OUT` (x86) | CPU — polling |
| E/S por interrupção | `IN`/`OUT` + ISR | CPU — acionada por interrupção |
| DMA | — | controlador DMA, sem CPU |

### E/S mapeada em portas (x86)

```asm
IN  AL, 0x60    ; lê do port 0x60 (teclado PS/2)
OUT 0x60, AL    ; escreve no port 0x60
```

### E/S mapeada em memória

Registradores do dispositivo aparecem como endereços de memória. MOV normal acessa o dispositivo. Usado por ARM e a maioria das arquiteturas RISC.

Ver [[DMA]] e [[Interrupção]] para mecanismos de transferência em volume e acionamento assíncrono.

---

## §5.5.8 — Outras Instruções

### Conversão

| Instrução | Operação |
|---|---|
| `MOVSX dst, src` | move com extensão de sinal (sign-extend) |
| `MOVZX dst, src` | move com extensão de zero (zero-extend) |
| `CVTSI2SD` | converte inteiro → FP double |
| `CVTTSD2SI` | converte FP double → inteiro (truncando) |
| `CBW / CWDE / CDQE` | extend AL→AX→EAX→RAX com bit de sinal |

### Sem operação e controle

| Instrução | Operação |
|---|---|
| `NOP` | nenhuma operação (1 ciclo); usado para alinhamento, timing, placeholder de patch |
| `HLT` | para a CPU; retoma na próxima interrupção; modo kernel |
| `PAUSE` | hint para spin-loop: reduz consumo e evita penalidade de branch em hyper-threading |

### Barreiras de memória (memory fence)

Em CPUs com reordenação de memória (OoO, cache write-buffer), barreiras impedem que loads/stores cruzem o ponto de sincronização.

| Instrução | Barreira |
|---|---|
| `MFENCE` | full fence: ordena todos os loads e stores anteriores vs. posteriores |
| `SFENCE` | store fence: ordena stores (writes) |
| `LFENCE` | load fence: ordena loads (reads) |
| `LOCK` (prefixo) | garante atomicidade de RMW (read-modify-write) em barramento |

Ver [[Semântica de memória na ISA|ISA#Semântica de memória]] e [[Concorrência e Paralelismo]] para contexto de uso.

---

## Ver também

- [[ISA]] — modelo de memória, registradores, PSW, modos de operação
- [[Modos de Endereçamento]] — como os operandos são especificados
- [[Formatos de Instrução]] — codificação binária das instruções
- [[Flag]] — PSW/EFLAGS, CMP/TEST, SET_cc
- [[Pilha]] — CALL/RET, quadro de pilha, recursão, corotinas
- [[Previsão de Desvio]] — hardware que trata desvios condicionais
- [[Instruções SIMD]] — vetorização (ADD em múltiplos elementos simultâneos)
- [[DMA]] — E/S de alto volume sem instrução IN/OUT
- [[Interrupção]] — E/S por interrupção
- [[Concorrência e Paralelismo]] — MFENCE/LOCK em contexto de threads