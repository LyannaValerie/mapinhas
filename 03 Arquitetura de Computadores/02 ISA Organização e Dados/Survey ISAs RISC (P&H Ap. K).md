---
title: Survey ISAs RISC (P&H Ap. K)
aliases:
  - survey RISC
  - comparativo RISC
  - ARMv8 vs MIPS64
  - ISAs modernas
tags:
  - computação/arquitetura
date: 2026-04-22
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Survey ISAs RISC (P&H Apêndice K)

## Definição

Levantamento comparativo de 8 [[ISA|ISAs]] [[RISC e CISC|RISC]] modernas, organizado em dois grupos:
- **Desktop/server/PMD:** ARMv8 (AArch64), MIPS64 v6, Power v3.0, RISC-V (RV64G), SPARC v9
- **Embedded (16+32 bits):** Thumb-2, microMIPS64, RV64GC

## Contexto

Apêndice K de Patterson & Hennessy demonstra que arquiteturas RISC modernas são **notavelmente similares** entre si — resultado de convergência ao longo de décadas de pesquisa e competição de mercado. O contraste com ISAs CISC (como [[VAX — Arquitetura CISC|VAX]] e [[IBM 360-370 — ISA|IBM 360]]) é marcante.

## Grupo 1 — Desktop/Server/PMD

### Resumo comparativo

| Propriedade | ARMv8 (AArch64) | MIPS64 v6 | Power v3.0 | RV64G | SPARC v9 |
|---|---|---|---|---|---|
| Registradores inteiro | 32 (R31=0 ou SP) | 32 (R0=0) | 32 (R0=0) | 32 (R0=0) | 32 (R0=0) |
| Registradores FP | 32 × 64 bits | 32 × 64 bits | 32 × 64 bits | 32 × 64 bits | 32 × 64 bits |
| Comprimento de instrução | 32 bits (fixo) | 32 bits (fixo) | 32 bits (fixo) | 32 bits (fixo) | 32 bits (fixo) |
| Endianness | Bi-endian | Bi-endian | Bi-endian | Little-endian | Big-endian |
| Alinhamento obrigatório | Sim | Sim | Sim | Sim | Sim |
| Modos de endereçamento de dados | 3–4 | 1 | 2 | 1 | 2 |
| Cond. codes explícitos | Não (flags separados) | Não | Não | Não | Sim (ICC) |

> [!note] Convergência RISC
> Exceto pelo número de modos de endereçamento e alguns detalhes de instrução, os conjuntos de instruções inteiras dessas arquiteturas são muito similares.

### ARMv8 — AArch64

- Registrador 31: zero quando em ALU/lógica; stack pointer quando em load/store
- Sem execução condicional generalizada (removida do ARMv7 → ARMv8)
- Suporte a instruções de campo de bits, sincronização
- Thumb-2 (16+32 bits) para código mais compacto — versão de 32 bits reduz código a ~73% do ARMv7

### MIPS64 v6

- R0 sempre zero
- Branch/jump com offsets em 32-bit boundaries
- microMIPS64: offsets de branch/jump interpretados como 16-bit aligned
- microMIPS64 usa espaço de encoding reservado em MIPS64 para extensões definidas pelo usuário

### RISC-V (RV64G)

- Planejado com a extensão comprimida (C) desde o início → branches e jumps em 16-bit boundaries
- RV64GC com 16-bit instructions: código cai para ~73% do tamanho com apenas 32-bit instructions
- RV32E: versão com 16 registradores + 16-bit instructions + sem FP (embedded muito restrito)

### Power v3.0

- Fusion de IBM Power e PowerPC
- R0 como zero em cálculos de endereço (pode ser modificado por operações ALU no PowerPC)
- Register indirect = displacement com offset 0
- Absolute (alcance limitado) = displacement com R0 como base

### SPARC v9

- Único com condition codes explícitos (ICC — integer condition codes)
- Big-endian nativo
- Sem R0 implícito em modos de endereço — absolute sintetizado via R0 + displacement

## Grupo 2 — Embedded (16-bit extensions)

### Objetivo

Reduzir tamanho de código de programa substituindo instruções de 32 bits por versões de 16 bits para operações comuns. Especialmente útil em Flash limitada de sistemas embutidos.

| Propriedade | Thumb-2 | microMIPS64 | RV64GC |
|---|---|---|---|
| Base de 32 bits | ARMv7 + subset ARMv8 | MIPS64 | RV64G (exato) |
| Registradores em 16-bit insns | 8 (R0–R7) + especiais | 8 + especiais | 8 + especiais |
| Stack pointer | R13 (SP) | R29 | R2 |
| Global pointer | — | R30 | — |
| Redução de tamanho de código | ~73% de ARMv7 | similar | ~73% de RV64G |

### Thumb-2 — diferenças do ARMv7

- 32-bit instructions = **subset** do ARMv7 (não idêntico)
- Remove execução condicional generalizada e `PC` como GPR de destino
- Adiciona instruções de ARMv8: field manipulation, system insns, sincronização
- ISA de 32-bit própria (não é puro ARMv7)

### microMIPS64

- Base = MIPS64 com 1 mudança: offsets de branch/jump em 16-bit boundaries
- Usa espaço de encoding reservado — incompatível com extensões user-defined de MIPS64

### RV64GC

- Mais limpo: base = RV64G exato, 16-bit instructions adicionais planejadas desde início
- Não requer mudanças na ISA base como microMIPS64

## Modos de Endereçamento

### Desktop/Server

- **Displacement (base + offset):** modo principal em todos
- **Register indirect:** sintetizado como displacement com offset 0
- **Absolute (range limitado):** sintetizado com R0 como base
- ARM: inclui dois tipos de modos com update (pre/post-increment)
- Power: inclui version do modo update de ARM
- Referências a código: PC-relative (branch offsets × 4 antes de somar ao PC para ampliar range)

### Embedded (16-bit instructions)

- Acesso a apenas 8 registradores como base registers
- Campos immediate muito reduzidos (displacement curto)
- Stack pointer como base register especial (acessível por instruções dedicadas)

## Implicações de Design

> [!info] Por que as RISCs convergiram
> Pressões de: (1) facilidade de pipelining, (2) compiladores otimizantes, (3) compatibilidade binária crescente. Arquiteturas que divergiram mais (SPARC condition codes, ARM predicated execution) sofreram pressão para simplificar em versões recentes.

### Tendência histórica
- MIPS16 (1995) → microMIPS (substituição) → microMIPS64
- Thumb (1996, apenas 16-bit) → Thumb-2 (16+32-bit, mais poderoso)
- RISC-V nasceu com C extension planejada desde o início — design limpo retroativamente

## Arquiteturas históricas relacionadas

| ISA | Empresa | Período | Mais similar a |
|---|---|---|---|
| DEC Alpha | DEC | 1992–2004 | MIPS64 |
| HP PA-RISC | HP | 1986–2005 | Power ISA |
| Itanium (IA-64) | Intel/HP | 2001+ | — (VLIW/EPIC, ver [[IA-64 e EPIC]]) |

## Ver também

- [[ISA]]
- [[RISC e CISC]]
- [[ARM]]
- [[Formatos de Instrução]]
- [[Modos de Endereçamento]]
- [[VAX — Arquitetura CISC]]
- [[IBM 360-370 — ISA]]
- [[IA-64 e EPIC]]
- [[Instruções SIMD]]
- [[Tipos de Instrução ISA]]
