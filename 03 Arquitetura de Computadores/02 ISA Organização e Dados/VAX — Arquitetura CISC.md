---
title: VAX — Arquitetura CISC
aliases:
  - VAX
  - VAX-11
  - Virtual Address Extension
  - arquitetura VAX
tags:
  - computação/arquitetura
  - computação/histórico
date: 2026-04-22
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# VAX — Arquitetura CISC

> VAX: the most successful minicomputer design in industry history … especially noted for its large, assembler-programmer-friendly instruction set—an asset that became a liability after the RISC revolution.
> — Eric Raymond, *The New Hacker's Dictionary* (1991)

## Definição

VAX (Virtual Address Extension) é a [[ISA]] [[RISC e CISC|CISC]] de 32 bits da DEC (Digital Equipment Corporation), lançada em 1978 como extensão da linha PDP-11. Representa o paradigma CISC levado ao extremo: instruções potentes, modos de endereçamento ortogonais e código compacto — ao custo de implementação complexa e alto [[Tempo de CPU|CPI]].

## Relações (SPO)

- VAX → sucessor de → [[Intel x86|PDP-11]] (DEC)
- VAX → representa → filosofia CISC: simplificar compilador, reduzir tamanho de código
- VAX → contrasta com → [[ISA|MIPS/RISC]]: pipeline simples, muitos registradores, instruções fixas
- VAX → influenciou → debate RISC vs. CISC dos anos 1980–1990
- VAX → substituído por → Alpha (DEC RISC, 1992)

## Contexto Histórico

Na época do projeto VAX (~1977), duas restrições dominavam:
1. **DRAMs de baixa capacidade** — menos de 1/1000 da capacidade atual → tamanho de código era crítico
2. **Compiladores simples** — filosofia predominante: ISA deve ser próxima de linguagens de alto nível

O arquiteto William Strecker enunciou quatro decisões centrais:
1. Tratamento regular e consistente de operadores
2. Evitar instruções que compiladores raramente gerariam
3. Incluir formas comuns de operadores
4. Substituir sequências de instruções comuns por instruções únicas

Resultado: ISA poderosa e densa, mas com CPI elevado e implementação custosa.

## Registradores

16 registradores de 32 bits (r0–r15), mas 4 reservados pela ISA:

| Registrador | Alias | Função |
|---|---|---|
| r12 | ap | Argument Pointer — base dos parâmetros na pilha |
| r13 | fp | Frame Pointer — base das variáveis locais |
| r14 | sp | Stack Pointer |
| r15 | pc | Program Counter (mapeado no arquivo de registradores) |

> [!note] PC como registrador
> r15 é o PC — desvios podem ser realizados com qualquer instrução da ULA que use r15 como destino. Isso sintetiza 4 dos 22 modos de endereçamento do VAX a partir dos demais.

## Tipos de Dados

| Bits | Tipo | Nome MIPS | Nome VAX |
|---|---|---|---|
| 8 | Inteiro | Byte | Byte |
| 16 | Inteiro | Half word | **Word** |
| 32 | Inteiro | **Word** | Long word |
| 32 | FP | Single precision | F_floating |
| 64 | Inteiro | Double word | Quad word |
| 64 | FP | Double precision | D_floating / G_floating |
| 8n | String | — | Character |

> [!warning] Terminologia divergente
> "Word" no VAX = 16 bits. No contexto MIPS/padrão = 32 bits. A primeira letra do tipo (b, w, l, f, q, d, g, c) é sufixo de instrução: `movb`, `movw`, `movl`, `movf`, `movq`, etc.

## Modos de Endereçamento

O VAX separa especificação do modo da especificação da operação — cada operando carrega seu próprio especificador de 1 byte (+ campos adicionais conforme necessário):

| Modo | Sintaxe | Exemplo | Significado | Bytes |
|---|---|---|---|---|
| Literal | `#value` | `#-1` | Constante de 6 bits com sinal | 1 |
| Immediate | `#value` | `#100` | Constante de largura do tipo | 1 + len(imm) |
| Register | `rn` | `r3` | r3 | 1 |
| Register deferred | `(rn)` | `(r3)` | M[r3] | 1 |
| Byte/word/long displacement | `disp(rn)` | `100(r3)` | M[r3 + 100] | 1 + len(disp) |
| Displacement deferred | `@disp(rn)` | `@100(r3)` | M[M[r3 + 100]] | 1 + len(disp) |
| Indexed (scaled) | `base[rx]` | `(r3)[r4]` | M[r3 + r4 × d] | 1 + len(base) |
| Autoincrement | `(rn)+` | `(r3)+` | M[r3]; r3 += d | 1 |
| Autodecrement | `-(rn)` | `-(r3)` | r3 -= d; M[r3] | 1 |
| Autoincrement deferred | `@(rn)+` | `@(r3)+` | M[M[r3]]; r3 += d | 1 |

`d` = tamanho do tipo em bytes (4 para long word).

**Endereçamento indexado (scaled):** multiplica o índice pelo tamanho do tipo automaticamente — elimina a necessidade de instrução `sll` + `add` presente no MIPS.

**Três tamanhos de deslocamento** (byte, word, long): endereços curtos ficam em 1 byte; não há instrução equivalente ao `lui` do MIPS — qualquer endereço é expresso diretamente.

## Codificação de Instruções

Formato variável: `opcode(1 byte) | especificador₁ | ... | especificadorₙ`

Cada especificador: 4 bits de modo + 4 bits de registrador (+ bytes adicionais se necessário).

### Exemplo: `addl3 r1, 737(r2), (r3)[r4]`

| Endereço | Conteúdo | Significado |
|---|---|---|
| 201 | `c1h` | Opcode addl3 |
| 202 | `44h` | Modo index, registrador r4 |
| 203 | `63h` | Register indirect (r3) |
| 204 | `c2h` | Word displacement, base r2 |
| 205–206 | `e1h 02h` | Deslocamento 737 (16 bits) |
| 207 | `51h` | Register mode, r1 (destino) |

Comprimento total: 7 bytes. Um `add` inteiro varia de 3 a 19 bytes.

> [!info] Implicação para implementação
> O VAX deve decodificar o primeiro operando antes de saber onde começa o segundo. Implementadores ficam tentados a gastar 1 ciclo por decodificação de operando → CPI elevado mesmo com endereços simples.

## Operações

### Convenção de nomes
```
(operação)(tipo)(n_operandos)
```
Exemplos: `addb2`, `addb3`, `addw2`, `addl3`, `addf2`, `addd3`.

Com todos os modos e tipos (byte/word/long): **mais de 30.000 versões de inteiro add** no VAX. MIPS tem 4.

### Instruções especiais de conveniência

| Instrução | Exemplo | Significado |
|---|---|---|
| `clr*` | `clrl r3` | r3 = 0 |
| `inc*` | `incl r3` | r3 = r3 + 1 |
| `dec*` | `decl r3` | r3 = r3 − 1 |
| `push*` | `pushl r3` | sp −= 4; M[sp] = r3 |
| `mova*` | `moval r7, 8(ap)` | Move o **endereço** do operando (não o valor) |

`pushl r3` = `movl -(sp), r3` — 1 byte mais curto por implicar sp.

### Branches e condition codes

Condition codes: N (Negative), Z (Zero), V (oVerflow), C (Carry) — setados como side effect da maioria das instruções.

Branches condicionais: `beql`, `bneq`, `blss`, `bleq`, `bgtr`, `bgeq`. Branches incondicionais por tamanho de deslocamento: `brb` (8 bits), `brw` (16 bits).

### Procedure call/return

`calls n, addr` — salva ap, fp, pc + registradores marcados na máscara de 16 bits no início da procedure + ajusta ap/fp/sp.
`ret` — restaura tudo salvo por `calls`.

A máscara fica no início da procedure (callee define o que salvar; caller executa o save). Ex:
```vax
.word ^m<r0,r1,r2,r3>   ; máscara: salvar r0–r3
```

## Exemplo Prático: `swap`

```c
void swap(int v[], int k) {
    int temp = v[k];
    v[k] = v[k+1];
    v[k+1] = temp;
}
```

**MIPS (17 linhas):**
```mips
swap: addi $29,$29,-12
      sw   $2, 0($29)
      sw   $15,4($29)
      sw   $16,8($29)
      muli $2, $5, 4
      add  $2, $4, $2
      lw   $15,0($2)
      lw   $16,4($2)
      sw   $16,0($2)
      sw   $15,4($2)
      lw   $2, 0($29)
      lw   $15,4($29)
      lw   $16,8($29)
      addi $29,$29,12
      jr   $31
```

**VAX (8 linhas):**
```vax
swap: .word ^m<r0,r1,r2,r3>
      movl r2, 4(ap)       ; r2 = v[]
      movl r1, 8(ap)       ; r1 = k
      movl r3, (r2)[r1]    ; r3 = v[k]
      addl3 r0, #1, 8(ap)  ; r0 = k+1
      movl (r2)[r1],(r2)[r0] ; v[k] = v[k+1]
      movl (r2)[r0], r3    ; v[k+1] = temp
      ret
```

O endereçamento indexado (`(r2)[r1]`) elimina a conversão de índice para endereço byte. O `calls`/`ret` elimina save/restore explícito. Parâmetros passados via pilha (não em registradores como no MIPS).

## VAX vs. MIPS — Trade-offs Centrais

| Aspecto | VAX (CISC) | MIPS (RISC) |
|---|---|---|
| Tamanho das instruções | Variável (1–19+ bytes) | Fixo (4 bytes) |
| Registradores de uso geral | 12 (4 reservados) | 32 |
| Modos de endereçamento | 22 | 3–4 |
| Linhas de código | Poucas | Muitas |
| CPI | Alto (decodificação complexa) | Baixo (pipeline simples) |
| Código de máquina | Compacto | Esparso |
| Implementação em hardware | Complexa, custosa | Simples, rápida |
| Pipeline eficiente | Difícil | Natural |

> [!warning] Falácia: menos instruções = mais rápido
> O VAX executa menos instruções para a mesma tarefa, mas cada instrução pode levar muitos ciclos. Desempenho = instruções × CPI × 1/clock. O MIPS ganha em CPI e clock, compensando o maior número de instruções.

## Legado

- Estudo de Bhandarkar e Clark (1991) comparou VAX e MIPS quantitativamente, confirmando vantagens do RISC mesmo após 10 anos de debate.
- Fim da linha VAX: substituída pelo Alpha (RISC de 64 bits, 1992).
- O VAX foi o "case study CISC" canônico — ensina por contraste o que o RISC simplificou.
- Influência indireta: processadores x86 modernos traduzem internamente CISC → micro-ops RISC antes de executar.

## Ver também

- [[ISA]]
- [[RISC e CISC]]
- [[Intel x86]]
- [[Modos de Endereçamento]]
- [[Formatos de Instrução]]
- [[Tempo de CPU]]
- [[Pipeline]]
- [[História da Computação]]
