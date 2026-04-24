---
title: Sistema de Compilação
aliases:
  - compilação
  - compilador
  - compilation system
  - pré-processador
  - preprocessor
  - linker
  - ligador
  - arquivo objeto
  - objeto relocável
  - gcc
tags:
  - computação/fundamentos
date: 2026-04-06
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Sistemas de Software]]

# Sistema de Compilação

## Definição
Conjunto de programas que traduz um programa em **linguagem de alto nível** (ex: C) em um **arquivo executável** em [[Linguagem de Máquina]], pronto para ser carregado na [[Memória]] e executado pelo [[Processador]]. Implementação concreta do conceito de [[Tradução e Interpretação|tradução]].

## Relações (SPO)
- Sistema de Compilação → implementa → [[Tradução e Interpretação|tradução]] de nível 5 para nível 1/2
- Sistema de Compilação → produz → arquivo executável em [[Linguagem de Máquina]]
- Sistema de Compilação → composto por → pré-processador, compilador, assembler, linker
- gcc → driver → do sistema de compilação no Unix/Linux

---

## O pipeline de compilação

```
hello.c          hello.i          hello.s         hello.o
(código C)  →cpp→ (C modificado) →cc1→ (assembly) →as→ (obj) →ld→  hello
  texto          texto            texto           binário         executável
```

### Fase 1 — Pré-processamento (cpp)

O **pré-processador** (`cpp`) processa diretivas que começam com `#`:
- `#include <stdio.h>` → o pré-processador lê o arquivo `stdio.h` e insere seu conteúdo diretamente no código
- `#define` → substituições de macros (ver abaixo)
- `#if` / `#ifdef` / `#endif` → compilação condicional
- Resultado: arquivo `.i` — ainda código C, mas com todas as diretivas processadas

#### `#define` — Constantes simbólicas

```c
#define LOWER  0    /* limite inferior da tabela */
#define UPPER  300  /* limite superior */
#define STEP   20   /* passo */
```

Após o `#define`, toda ocorrência de `LOWER` (fora de aspas e não parte de outro nome) é substituída por `0` no código antes da compilação.

**Regras:**
- Nomes de constantes simbólicas são convencionalmente escritos em **MAIÚSCULAS** para distingui-los de variáveis
- `#define` **não termina com ponto-e-vírgula** (diferente de declarações C)
- O valor de substituição pode ser qualquer sequência de caracteres — não se limita a números
- Constantes simbólicas **não aparecem em declarações** de variáveis (`int LOWER` seria erro)

> [!tip] Por que evitar "magic numbers"?
> Enterrar valores literais como `300` e `20` num programa dificulta a leitura e manutenção. `#define UPPER 300` torna o código autodocumentado e permite alterar o valor em um único lugar.

### Fase 2 — Compilação (cc1)

O **compilador** (`cc1`) traduz o arquivo `.i` (C) para o arquivo `.s` ([[Linguagem Assembly|assembly]]):

```asm
main:
    subq  $8, %rsp
    movl  $.LC0, %edi
    call  puts
    movl  $0, %eax
    addq  $8, %rsp
    ret
```

> [!info] Por que assembly como intermediário?
> Assembly é uma linguagem comum de saída para compiladores de diferentes linguagens de alto nível — compiladores de C e de Fortran geram o **mesmo** assembly para a mesma arquitetura.

### Fase 3 — Montagem (as)

O **assembler** (`as`) traduz o arquivo `.s` para **[[Linguagem de Máquina|instruções de máquina]]** em binário e empacota o resultado em um **arquivo objeto relocável** (`.o`).

- O arquivo `.o` é binário — se aberto num editor de texto, parece "ruído"
- "Relocável" = o código ainda não sabe onde será carregado na memória

### Fase 4 — Ligação (ld)

O **linker** (`ld`) **mescla** arquivos `.o` para produzir o executável final:
- O programa `hello.o` chama `printf` — mas `printf` está em `printf.o` (biblioteca padrão C, pré-compilada)
- O linker combina `hello.o` + `printf.o` (e outros) em um único executável `hello`
- Resultado: arquivo binário pronto para execução

> [!note] Por que separar em 4 fases?
> Separação de responsabilidades: cada fase tem uma transformação bem definida. Também permite **compilação incremental** — se apenas um arquivo `.c` mudar, só ele precisa ser recompilado; os demais `.o` são reutilizados na fase de linker.

---

## Relação com os Níveis de Abstração

| Fase | De | Para | Nível (Tanenbaum) |
|---|---|---|---|
| Pré-processamento | Código C com diretivas | Código C puro | Nível 5 → Nível 5 |
| Compilação | C (nível 5) | Assembly (nível 4) | Nível 5 → Nível 4 |
| Montagem | Assembly (nível 4) | Código objeto (nível 2) | Nível 4 → Nível 2 |
| Ligação | Múltiplos `.o` | Executável completo | Nível 2 → pronto para execução |

---

## gcc — o driver de compilação

No Unix/Linux, o comando `gcc` orquestra todas as fases automaticamente:

```bash
gcc -o hello hello.c
```

Parte do projeto [[Linguagem C#GNU — as ferramentas de C para o mundo livre|GNU]] (Richard Stallman). Suporta múltiplas linguagens: C, C++, Fortran, Java, Pascal, Ada, Objective-C.

---

---

## Por que entender o sistema de compilação? (CS:APP §1.3)

Para programas simples, podemos confiar no compilador. Mas há três razões importantes para entender o que acontece por baixo:

### 1. Otimizar desempenho
Sem conhecer os internos do compilador, não é possível tomar boas decisões de codificação. Exemplos de perguntas que exigem conhecimento de compilação:
- `switch` é sempre mais eficiente que `if-else`?
- Qual o overhead de uma chamada de função?
- `while` é mais eficiente que `for`?
- Referências por ponteiro são mais eficientes que índices de array?
- Por que somar em uma variável local é mais rápido do que em um parâmetro passado por referência?
- Como rearranjar parênteses numa expressão aritmética pode acelerar uma função?

### 2. Entender erros de ligação (*link-time errors*)
Erros do linker figuram entre os mais confusos da programação:
- O que significa "cannot resolve reference"?
- Qual a diferença entre variável `static` e variável global?
- O que acontece se dois arquivos `.c` definem uma variável global com o mesmo nome?
- Qual a diferença entre biblioteca estática e dinâmica?
- Por que a ordem das bibliotecas na linha de comando importa?
- Por que alguns erros de linker só aparecem em tempo de execução?

### 3. Evitar vulnerabilidades de segurança
**Buffer overflow** é uma das vulnerabilidades mais exploradas em servidores de rede e Internet. Ocorre porque programadores não restringem adequadamente a quantidade e o tipo de dados aceitos de fontes não confiáveis. Entender como dados e informações de controle são armazenados na **pilha de programa** é o primeiro passo para programação segura.

---

## Fluxo de execução — do código ao output (CS:APP §1.4.2)

O ciclo completo desde a digitação de um comando até a saída na tela envolve todos os subsistemas de hardware:

```
Teclado → Processador → Memória → (Shell carrega)
Disco ──[DMA]──────────→ Memória → Processador → Display
```

### Passo a passo com o programa `hello`

**1. Shell aguarda comando**
O [[Shell]] está rodando, aguardando entrada. O [[Processador]] executa suas instruções.

**2. Usuário digita `./hello`**
Cada caractere digitado no teclado é lido pelo [[Processador]] em um [[Registrador]] e então gravado na [[Memória]] principal — um byte por vez.

**3. Shell pressiona Enter — carrega o executável**
O shell identifica `./hello` como executável no disco. Emite sequência de instruções que dispara uma transferência via **[[DMA]]**: o código e dados do arquivo `hello` vão do disco diretamente para a [[Memória]], **sem passar pelo processador**.

**4. CPU executa o programa**
Com o executável na memória, o [[Processador]] começa a executar as instruções do `main`. As instruções copiam a string `hello, world\n` da [[Memória]] para o arquivo de [[Registrador|registradores]], e daí para o **dispositivo de display**.

**5. Output na tela**
A string aparece na tela. O programa encerra. O shell volta ao prompt.

> [!info] Três transferências de dados
> 1. Teclado → registrador → memória (digitação do comando)
> 2. Disco → memória via DMA (carregamento do executável)
> 3. Memória → registrador → display (execução e output)

---

## Ver também
- [[Tradução e Interpretação]] — conceito abstrato de tradução entre níveis
- [[Linguagem Assembly]] — saída da fase de compilação
- [[Linguagem de Máquina]] — saída da fase de montagem
- [[Linguagem C]] — a linguagem de entrada mais comum neste pipeline
- [[Níveis de Abstração]] — o compilador implementa a ponte entre níveis 5 e 4
- [[Shell]] — carrega e executa o produto do sistema de compilação
- [[DMA]] — mecanismo de carregamento do executável do disco para a memória
- [[Memória]] — onde o executável é carregado para execução
