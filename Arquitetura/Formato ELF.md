---
title: Formato ELF
aliases:
  - ELF
  - Executable and Linkable Format
  - arquivo objeto ELF
  - .text
  - .bss
  - .data
  - .symtab
  - .rel.text
  - seção ELF
  - objeto relocável
tags:
  - computação/arquitetura
date: 2026-04-14
---

# Formato ELF

## Definição
**Executable and Linkable Format** — formato padrão de arquivo objeto em sistemas Linux/Unix x86-64. Substitui a.out (Unix original), PE (Windows) e Mach-O (macOS). Usado pelo [[Ligador]], assembler e loader.

## Relações (SPO)
- ELF → define → formato dos três tipos de arquivo objeto no Linux x86-64
- Arquivo objeto relocável → combinado por → [[Ligador]] em executável
- Arquivo objeto executável → mapeado por → loader em memória virtual
- Arquivo objeto compartilhado → carregado por → ligador dinâmico em load time ou run time
- `.symtab` → contém → tabela de símbolos de todo módulo relocável
- `.rel.text` → contém → entradas de relocação para o código

## Três tipos de arquivo objeto

| Tipo | Extensão | Produzido por | Uso |
|---|---|---|---|
| **Relocável** | `.o` | Assembler | Combinado pelo [[Ligador]] |
| **Executável** | (sem extensão) | Ligador | Copiado para memória e executado |
| **Compartilhado** | `.so` | Ligador (`-shared`) | Ligado dinamicamente em load/run time |

## Estrutura — arquivo relocável

```
┌─────────────────────┐  offset 0
│ ELF header          │  tamanho, tipo, ISA, offset da section header table
├─────────────────────┤
│ .text               │  código de máquina compilado
├─────────────────────┤
│ .rodata             │  dados somente-leitura (strings de printf, tabelas de switch)
├─────────────────────┤
│ .data               │  variáveis globais e estáticas inicializadas
├─────────────────────┤
│ .bss                │  variáveis globais/estáticas não inicializadas (placeholder — sem bytes no arquivo)
├─────────────────────┤
│ .symtab             │  tabela de símbolos (funções e globais definidos/referenciados)
├─────────────────────┤
│ .rel.text           │  entradas de relocação para .text
├─────────────────────┤
│ .rel.data           │  entradas de relocação para .data
├─────────────────────┤
│ .debug              │  símbolos de depuração (somente com -g)
├─────────────────────┤
│ .line               │  mapeamento linha-fonte ↔ instrução (somente com -g)
├─────────────────────┤
│ .strtab             │  strings de nomes de símbolos e seções (null-terminadas)
├─────────────────────┤
│ Section header table│  localização e tamanho de cada seção
└─────────────────────┘
```

## Seções principais

| Seção | Conteúdo | Observação |
|---|---|---|
| `.text` | Código de máquina | — |
| `.rodata` | Constantes somente-leitura | Strings de `printf`, tabelas de `switch` |
| `.data` | Variáveis globais/estáticas **inicializadas** | Variáveis locais ficam na pilha — não aqui |
| `.bss` | Variáveis globais/estáticas **não inicializadas** ou = 0 | Sem bytes no arquivo; zeradas em runtime |
| `.symtab` | Tabela de símbolos | Presente mesmo sem `-g`; removível com `strip` |
| `.rel.text` | Relocações para `.text` | Chamadas externas e referências a globais |
| `.rel.data` | Relocações para `.data` | Ponteiros globais inicializados com endereço de função/variável |
| `.debug` | Símbolos de depuração | Somente com `gcc -g` |
| `.line` | Mapeamento linha ↔ instrução | Somente com `gcc -g` |
| `.strtab` | Strings de nomes | Sequência de strings terminadas em `\0` |

> [!note] .bss — "Better Save Space"
> Variáveis não inicializadas não ocupam espaço no arquivo objeto. O SO as aloca como zeros em runtime. Sigla vem do IBM 704 assembly ("block started by symbol", ~1957).

## Entrada na tabela de símbolos (`Elf64_Symbol`)

```c
typedef struct {
    int   name;      // offset no .strtab — aponta para string do nome
    char  type:4,    // FUNC ou OBJECT (4 bits)
          binding:4; // LOCAL ou GLOBAL (4 bits)
    char  reserved;
    short section;   // índice na section header table; ou ABS/UNDEF/COMMON
    long  value;     // offset na seção (relocável) ou endereço absoluto (executável)
    long  size;      // tamanho em bytes
} Elf64_Symbol;
```

### Pseudoseções especiais (apenas em arquivos relocáveis)

| Pseudoseção | Significado |
|---|---|
| `ABS` | Símbolo que não deve ser relocado (ex: número de seção) |
| `UNDEF` | Símbolo referenciado aqui, definido em outro módulo |
| `COMMON` | Variável global não inicializada (fraca); `value` = alinhamento, `size` = tamanho mínimo |

### COMMON vs .bss

| Símbolo | Seção |
|---|---|
| Variável global não inicializada | `COMMON` |
| Variável estática não inicializada | `.bss` |
| Variável global/estática inicializada a zero | `.bss` |

Razão: variáveis `COMMON` podem ter múltiplas definições fracas em módulos diferentes — o ligador precisa comparar tamanhos e escolher o maior. Variáveis `static` são únicas por construção.

## Entrada de relocação (`Elf64_Rela`)

```c
typedef struct {
    long offset;    // offset na seção onde a referência está
    long type:32,   // tipo de relocação
         symbol:32; // índice na tabela de símbolos
    long addend;    // constante somada à referência (ex: -4 para PC-relativo)
} Elf64_Rela;
```

### Tipos de relocação (x86-64 small code model)

| Tipo | Mecanismo | Uso típico |
|---|---|---|
| `R_X86_64_PC32` | Endereço relativo ao PC de 32 bits | `call` para função externa |
| `R_X86_64_32` | Endereço absoluto de 32 bits | `mov` com endereço de variável global |

O small code model (padrão do gcc) assume executável < 2 GB. Para programas maiores: `-mcmodel=medium` ou `-mcmodel=large`.

## Estrutura — executável ELF

O executável adiciona um **segment header table** que descreve como mapear seções em segmentos de memória:

```
┌─────────────────────┐
│ ELF header          │  inclui entry point (_start)
├─────────────────────┤
│ Segment header table│  descreve segmentos para o loader
├─────────────────────┤
│ .init               │  código de inicialização (_init)
│ .text               │  segmento r-x (código)
│ .rodata             │
├─────────────────────┤
│ .data               │  segmento rw- (dados)
│ .bss                │
├─────────────────────┤
│ .symtab             │  não carregados em memória
│ .debug              │
│ .strtab             │
├─────────────────────┤
│ Section header table│
└─────────────────────┘
```

**Restrição de alinhamento**: para qualquer segmento, `vaddr mod align = off mod align` (align = 2²¹ = 2 MB). Otimização para transferência eficiente via memória virtual.

**Diferença filesz vs memsz**: `memsz` pode ser maior que `filesz` — a diferença corresponde ao `.bss`, zerado em runtime sem bytes no arquivo.

## Ferramentas de inspeção

```bash
readelf -a main.o          # estrutura ELF completa
readelf -s main.o          # só tabela de símbolos
objdump -dx main.o         # desmontagem + entradas de relocação inline
nm main.o                  # tabela de símbolos (formato compacto)
size main.o                # tamanhos das seções
```

## Ver também
- [[Ligador]] — usa ELF para resolver símbolos e relocar
- [[PIC e GOT]] — GOT e PLT residem nas seções ELF de dados/código
- [[Processo de Montagem]] — assembler produz arquivos ELF relocáveis
- [[Sistema de Compilação]] — pipeline que gera arquivos ELF
- [[Memória Virtual]] — como segmentos ELF são mapeados em espaço virtual
- [[Linguagem de Máquina]] — conteúdo da seção `.text`
