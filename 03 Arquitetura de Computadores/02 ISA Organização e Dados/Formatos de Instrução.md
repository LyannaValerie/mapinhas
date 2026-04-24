---
title: Formatos de Instrução
aliases:
  - instruction formats
  - formato de instrução
  - expansão de opcode
  - opcode expansion
tags:
  - computação/arquitetura
date: 2026-04-13
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Formatos de Instrução

## Definição
Estrutura binária de uma instrução [[ISA]]: define quantos bits existem, como são divididos entre opcode e campos de endereço, e quantos operandos são especificados. O formato determina diretamente o tamanho do código, a velocidade de decodificação e o espaço de endereçamento disponível.

## Relações (SPO)
- Formato de instrução → divide-se em → opcode + campos de endereço
- Opcode → identifica → operação a executar
- Campo de endereço → especifica → [[Modos de Endereçamento|modo e localização do operando]]
- Instrução mais curta → reduz → uso de memória e largura de banda
- Instrução mais longa → permite → mais operandos e maior espaço endereçável

---

## Quantidade de Endereços por Instrução

| Tipo | Campos | Exemplo | Observação |
|---|---|---|---|
| **3 endereços** | opcode + src1 + src2 + dst | `ADD R1, R2, R3` | Típico em RISC; instrução longa |
| **2 endereços** | opcode + src/dst + src | `ADD R1, R2` | dst = src1; padrão no x86 |
| **1 endereço** | opcode + operando | `LOAD A` | Operando implícito no acumulador |
| **0 endereços** | opcode puro | `IADD` | Operandos implícitos na [[Pilha]] |

> [!note] Comprimento vs. quantidade de instruções
> Arquitetura de 3 endereços gera código mais curto (menos instruções por tarefa), mas cada instrução ocupa mais bits. Arquitetura de 0 endereços tem instruções compactas, mas precisa de mais instruções por operação.

---

## Relação entre Comprimento de Instrução e Palavra

Três abordagens comuns:

1. **Instrução = palavra** — simples, facilita alinhamento; desperdiça bits quando a instrução é simples
2. **Instrução menor que palavra** — múltiplas instruções por palavra (ex: 3 × 16 bits numa palavra de 48 bits)
3. **Comprimento variável** — instrução ocupa 1–6 bytes conforme o opcode (ex: x86); decodificação complexa

---

## Critérios de Projeto de Formato

### 1. Instruções curtas
- Programas mais compactos em memória
- Maior taxa de instruções por segundo dado o limite de largura de banda da cache:
  `instruções/s ≤ largura_de_banda_cache / comprimento_médio_instrução`
- Processadores superescalares buscam múltiplas instruções por ciclo — instruções menores facilitam

### 2. Espaço suficiente para opcodes
- Máquina com 2ⁿ operações exige ≥ n bits de opcode
- Sempre reservar opcodes livres para extensões futuras — arquiteturas que esgotam o espaço ficam presas

### 3. Resolução de endereço
- Memória endereçável por byte exige mais bits de endereço que por palavra
- Trade-off: endereço de byte → melhor resolução → instrução maior
- Exemplo: memória de 4 GB endereçada por byte = 32 bits de endereço; por palavra de 4 bytes = 30 bits

---

## Expansão de Opcodes

Técnica para usar o mesmo número total de bits da instrução, mas distribuir diferentemente os bits entre opcode e endereços conforme a instrução precise de mais ou menos operandos.

### Exemplo canônico (instrução de 16 bits, endereços de 4 bits)

Objetivo: codificar **15 instruções de 3 endereços**, **14 de 2**, **31 de 1** e **16 sem endereço**.

| Padrão nos bits 15–12 | Bits 11–8 | Endereços | Tipo |
|---|---|---|---|
| `0000`–`1110` | xxx yyyy zzzz | 3 endereços | 15 instruções |
| `1111` + `0000`–`1101` | yyyy zzzz | 2 endereços | 14 instruções |
| `1111` + `1110`–`1111` + `0000`–`1110` + extras | zzzz | 1 endereço | 31 instruções |
| `1111 1111 1111` + `0000`–`1111` | — | 0 endereços | 16 instruções |

```
Instrução de 3 endereços:  [opcode 4b][end1 4b][end2 4b][end3 4b]  → 15 opcodes (0000–1110)
Instrução de 2 endereços:  [1111][opcode 4b][end1 4b][end2 4b]     → 14 opcodes (0000–1101)
Instrução de 1 endereço:   [1111][1110 ou 1111][opcode 4b][end 4b] → 31 opcodes
Instrução de 0 endereços:  [1111][1111][1111][opcode 4b]            → 16 opcodes
```

**Princípio:** opcodes mais curtos para instruções que precisam de mais bits de endereço; opcodes mais longos para instruções sem endereços.

> [!tip] Dois usos da expansão
> 1. **Instrução de comprimento fixo:** opcode curto para instruções com muitos operandos, longo para instruções simples — tamanho total constante
> 2. **Minimizar comprimento médio:** opcode curto para instruções *frequentes*, longo para instruções *raras* — reduz tamanho médio do código

---

## Formatos dos Core i7, ARM e AVR

### Core i7 (CISC — comprimento variável, até 6 campos)

```
[PREFIXO 0–5B] [OPCODE 1–2B] [MOD/REG/RM 0–1B] [SIB 0–1B] [DESL. 0–4B] [IMM. 0–4B]
```

- Opcode pode ser 1 ou 2 bytes (byte `0xFF` = escape para opcode estendido)
- Byte MOD/REG/RM: 2 bits de modo + 3 bits de registrador destino + 3 bits de registrador/memória fonte
- Byte SIB: escala (2 bits) + índice (3 bits) + base (3 bits) — para [[Modos de Endereçamento#Endereçamento de Base Indexado|modo SIB]]
- Resultado: instruções de 1 a 15 bytes — decodificação muito complexa; dificulta implementações de alto desempenho

> [!warning] Legado histórico
> A irregularidade do x86 é consequência de décadas de extensões sobre o projeto original de 8 bits do 8088. Cada extensão foi adicionada mantendo compatibilidade total, acumulando exceções.

---

### ARM v7 / OMAP4430 (RISC — 32 bits fixos + Thumb de 16 bits)

```
[COND 4b][tipo 2b][opcode variável][Rn 4b][Rd 4b][operando2 12b]
```

- **Bits 28–31:** campo de condição presente em **toda** instrução → qualquer instrução pode ser predicada
- **Bits 26–27:** tipo de instrução (processamento de dados, carga/armazenamento, desvio...)
- **Thumb ISA:** versão de 16 bits das instruções; mesmas operações mas apenas 8 registradores e destino = fonte1

> [!info] Instrução predicada
> Toda instrução ARM carrega uma condição de 4 bits. Se a condição falhar (baseada nos flags do PSR), a instrução executa mas seu resultado é descartado — sem desvio, sem flush de pipeline.

---

### AVR / ATmega168 (RISC — 16 ou 32 bits, 6 formatos)

| Formato | Bits | Uso |
|---|---|---|
| 1 | 16 | ULA: 2 registradores (src + dst) |
| 2 | 16 | ULA estendida: 1 registrador (op. unária) |
| 3 | 16 | ULA + imediato de 8 bits sem sinal |
| 4 | 16 | Load/Store com imediato de 6 bits |
| 5 | 16 | Desvio relativo: imediato com sinal de 12 bits |
| 6 | **32** | Call/Jump com imediato de 22 bits |

- Registradores de 8 bits → limitação: imediatos também de 8 bits no máximo em instruções de 16 bits
- Apenas 4 instruções usam o formato 3 (opcode reduzido para acomodar imediato de 8 bits)

---

## Ver também
- [[Modos de Endereçamento]]
- [[ISA]]
- [[RISC e CISC]]
- [[Intel x86]]
- [[ARM]]
- [[AVR]]
- [[Registradores x86]]
