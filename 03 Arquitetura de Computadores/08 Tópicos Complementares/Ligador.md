---
title: Ligador
aliases:
  - linker
  - loader
  - ligação
  - carregamento
  - relocação
  - ligação dinâmica
  - módulo objeto
  - DLL
  - biblioteca compartilhada
  - símbolo forte
  - símbolo fraco
  - biblioteca estática
  - interpositioning
  - interposicionamento
tags:
  - computação/arquitetura
date: 2026-04-13
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Ligador

## Definição
Programa que combina múltiplos **módulos-objeto** (produzidos independentemente por compiladores/assemblers) em um único **programa binário executável**. Resolve dois problemas: **relocação** e **referências externas**.

## Relações (SPO)
- Ligador → combina → módulos-objeto em programa executável
- Ligador → resolve → problema de relocação
- Ligador → resolve → referências externas entre módulos
- Módulo-objeto → produzido por → compilador ou assembler
- Ligação → não representa → mudança de nível (entrada e saída são para a mesma máquina virtual)
- DLL → implementa → ligação dinâmica no Windows
- Biblioteca compartilhada → implementa → ligação dinâmica no UNIX
- Símbolo forte → definido por → função ou variável global inicializada
- Símbolo fraco → definido por → variável global não inicializada
- Ligador → usa → [[Formato ELF]] em sistemas Linux x86-64

## Por que modularizar?

Cada módulo é traduzido separadamente. Ao alterar um procedimento, só ele precisa ser recompilado — não todos os módulos. A religação é muito mais rápida que a retradução. Crítico em programas com centenas de milhares de módulos.

```
Arquivo-fonte 1 ──┐
Arquivo-fonte 2 ──┤ Tradutor ──> Módulo-objeto N ──┐
Arquivo-fonte 3 ──┘                                 ├── Ligador ──> Executável
                                                    ┘
```

No Windows: módulos-objeto = `.obj`, executáveis = `.exe`.
No UNIX: módulos-objeto = `.o`, executáveis sem extensão.

## Problemas resolvidos pelo ligador

### Problema da relocação
Cada módulo-objeto assume que começa no endereço 0 (ILC inicia em 0 durante montagem). Ao fundir módulos em um único espaço de endereço, todos os endereços precisam ser ajustados.

### Problema da referência externa
Um módulo pode chamar procedimentos definidos em outros módulos (ex: `CALL B`). O assembler não conhece o endereço de `B` — só o ligador sabe após posicionar todos os módulos.

## Etapas de ligação

1. Construir tabela com nome, comprimento e endereço de início de cada módulo
2. Atribuir endereço de início a cada módulo (sequencialmente no espaço de endereço)
3. Para cada instrução que referencia memória: somar a constante de relocação do módulo
4. Para cada instrução que referencia símbolo externo: inserir o endereço real do símbolo

| Módulo | Comprimento | Endereço de início |
|---|---|---|
| A | 400 | 100 |
| B | 600 | 500 |
| C | 500 | 1.100 |
| D | 300 | 1.600 |

## Estrutura de um módulo-objeto

| Parte | Conteúdo |
|---|---|
| **Identificação** | Nome do módulo, comprimentos das partes, data de montagem |
| **Tabela de pontos de entrada** | Símbolos exportados (`PUBLIC`) com seus valores |
| **Tabela de referências externas** | Símbolos importados (`EXTERN`) e quais instruções os usam |
| **Código e constantes** | Único conteúdo carregado em memória para execução |
| **Dicionário de relocação** | Quais endereços precisam ser relocados (mapa de bits ou lista) |
| **Marca de fim** | Delimitador final, possivelmente checksum |

As partes 1, 2, 3, 5 e 6 são usadas pelo ligador e descartadas antes da execução.

## Tempo de vinculação

Momento em que nomes simbólicos são ligados a endereços físicos absolutos. Seis possibilidades:

| Momento | Descrição |
|---|---|
| Escrita do programa | Endereços fixos no código-fonte |
| Tradução | Compilador gera executável com endereços absolutos |
| Ligação (pré-carregamento) | Ligador tradicional — problema ao mover programa |
| Carregamento | Relocação feita ao carregar na memória |
| Carregamento do registrador de base | Endereçamento relativo a registrador |
| Execução da instrução | Endereço calculado dinamicamente em tempo de execução |

## Relocação dinâmica

Mecanismos que permitem mover programas na memória após a ligação:

**Paginação**: após mover o programa, só a tabela de páginas precisa ser atualizada — não o programa.

**Registrador de relocação em tempo de execução**: hardware soma o registrador a todo endereço de memória antes de acessar. Ao mover o programa, o SO atualiza apenas o registrador. Menos geral que paginação (programa move como unidade).

**Código independente de posição (PIC)**: todas as referências à memória são relativas ao contador de programa. Programa pode ser colocado em qualquer endereço sem relocação.

## Ligação dinâmica

Técnica na qual procedimentos são ligados apenas quando chamados pela primeira vez, não antes da execução do programa. Vantagem: código raramente usado (handlers de erro, compilação de construções raras) não precisa ser carregado antecipadamente.

### Ligação dinâmica no MULTICS

Cada programa tem um **segmento de ligação** com um bloco por procedimento chamável. Bloco contém: palavra reservada para endereço virtual (inicialmente inválido) + nome do procedimento como string.

Fluxo:
1. Chamada indireta através da palavra do segmento de ligação
2. Endereço inválido causa exceção → ligador dinâmico é invocado
3. Ligador busca o procedimento pelo nome no diretório de arquivos
4. Atribui endereço virtual ao procedimento, sobrescreve a palavra inválida
5. Reinicia a instrução que falhou — execução continua normalmente
6. Chamadas subsequentes ao mesmo procedimento não geram exceção (endereço já está válido)

### DLL no Windows

**Dynamic Link Library** — arquivo especial que contém procedimentos e/ou dados compartilháveis entre múltiplos processos simultaneamente.

Extensões: `.dll` (geral), `.drv` (drivers), `.fon` (fontes).

**Vantagens**:
- Economiza memória e disco: biblioteca aparece uma vez, usada por N processos
- Facilita atualização: novo `.dll` corrige bugs sem recompilar os programas usuários

**Dois modos de vinculação**:

| Modo | Descrição |
|---|---|
| **Implícita** | Programa ligado estaticamente a biblioteca de importação (`.lib`); DLLs carregadas automaticamente ao iniciar o processo |
| **Explícita** | Programa faz chamadas em tempo de execução para vincular/desvincular da DLL e obter endereços dos procedimentos |

> [!note] DLL não é processo nem thread
> Procedimento em DLL executa no thread chamador, usa a pilha do chamador. Comporta-se como procedimento ligado estaticamente — diferença está apenas na vinculação.

### Biblioteca compartilhada no UNIX

Equivalente às DLLs. Suporta apenas ligação implícita. Composta de:
- **Biblioteca hospedeira**: ligada estaticamente ao executável
- **Biblioteca-alvo**: chamada em tempo de execução

### GOT e PLT (Linux x86-64)

Bibliotecas compartilhadas compiladas com [[PIC e GOT|PIC]] usam dois mecanismos principais:
- **GOT** (Global Offset Table): tabela no segmento de dados com endereços absolutos de variáveis globais — preenchida pelo ligador dinâmico no carregamento
- **PLT** (Procedure Linkage Table): tabela no segmento de código que implementa *lazy binding* — endereço de função externa resolvido apenas na primeira chamada

Ver [[PIC e GOT]] para detalhes.

## Tipos de símbolos de ligação (ELF)

No contexto do ligador, há três categorias de símbolos em cada módulo-objeto `m`:

| Tipo | Visibilidade | Correspondência em C |
|---|---|---|
| **Global definido em m** | Outros módulos podem referenciar | Funções e variáveis globais sem `static` |
| **Global externo (extern)** | Referenciado em m, definido em outro módulo | Funções/variáveis definidas em outro `.o` |
| **Local de m** | Apenas dentro de m | Funções e variáveis globais com `static` |

> [!warning] Símbolo local de ligação ≠ variável local de programa
> Variáveis locais automáticas não têm entrada na tabela de símbolos — gerenciadas na pilha. Variáveis `static` dentro de funções têm entrada na `.symtab` e ficam em `.data` ou `.bss`.

Quando duas funções no mesmo módulo declaram `static int x`, o compilador exporta dois símbolos com nomes únicos (ex: `x.1` e `x.2`).

## Símbolos fortes e fracos

O compilador marca cada símbolo global como **forte** ou **fraco**:
- **Forte**: funções e variáveis globais **inicializadas**
- **Fraco**: variáveis globais **não inicializadas**

| Regra | Situação | Ação |
|---|---|---|
| 1 | Múltiplos símbolos fortes com mesmo nome | **Erro de ligação** |
| 2 | Um forte + um ou mais fracos com mesmo nome | Escolhe o **forte** silenciosamente |
| 3 | Múltiplos símbolos fracos com mesmo nome | Escolhe **qualquer um** silenciosamente |

> [!danger] Bug silencioso — tipos incompatíveis
> Se `x` é `int` em um módulo e `double x` (não inicializado) em outro, a regra 2 escolhe o `int` sem aviso. O `double` sobrescreve 8 bytes no endereço de `x`, corrompendo a variável vizinha em memória. Use `gcc -fno-common` ou `-Werror` para tornar isso erro explícito.

## Bibliotecas estáticas

Coleção de módulos-objeto empacotada em arquivo `.a` (formato *archive*):

```bash
gcc -c addvec.c multvec.c
ar rcs libvector.a addvec.o multvec.o         # cria .a
gcc -static -o prog main.o ./libvector.a      # liga estaticamente
gcc -static -o prog main.o -L. -lvector       # equivalente
```

Durante resolução, o ligador mantém três conjuntos (E, U, D) e processa arquivos da **esquerda para a direita**:
- **E**: módulos a incluir no executável
- **U**: símbolos referenciados mas não definidos ainda
- **D**: símbolos já definidos

Ao processar um `.a`, adiciona em E apenas os módulos que definem símbolos em U. O restante é descartado.

### Ordem na linha de comando

```bash
gcc -static libvector.a main.o   # ERRO: quando .a é processado, U está vazio
gcc -static main.o libvector.a   # OK
```

Dependências circulares exigem repetição:
```bash
gcc foo.c libx.a liby.a libx.a   # libx ↔ liby dependem mutuamente
```

## Algoritmo de relocação (ELF x86-64)

Após resolução de símbolos, o ligador mescla seções e atribui endereços de runtime. Para cada entrada de relocação `r`:

**PC-relativo** (`R_X86_64_PC32`):
```
refaddr = ADDR(seção) + r.offset
*refptr  = ADDR(r.symbol) + r.addend - refaddr
```

**Absoluto** (`R_X86_64_32`):
```
*refptr  = ADDR(r.symbol) + r.addend
```

Exemplo PC-relativo (`call sum`, `ADDR(.text)=0x4004d0`, `ADDR(sum)=0x4004e8`, `r.offset=0xf`, `r.addend=-4`):
```
refaddr = 0x4004d0 + 0xf = 0x4004df
*refptr = 0x4004e8 + (-4) - 0x4004df = 0x5
```
Ao executar: `PC = 0x4004e3`, `0x4004e3 + 0x5 = 0x4004e8` ✓

Ver [[Formato ELF]] para estrutura das entradas de relocação (`Elf64_Rela`).

## Imagem de memória em runtime (x86-64 Linux)

```
2^48-1   ─────────────────────────
          Pilha do usuário         ← %rsp (cresce para baixo)
          ...
          Libs compartilhadas
          ...
    brk  ─────────────────────────
          Heap (malloc)            (cresce para cima)
          Segmento de dados (.data, .bss)
0x400000  Segmento de código (.init, .text, .rodata)
0x000000  ─────────────────────────
          [kernel — acima de 2^48]
```

O carregador é invocado via `execve`: cria nova imagem de memória e salta para `_start` → `__libc_start_main` → `main`.

> [!info] Carregamento real usa mapeamento de páginas
> O SO não copia o executável imediatamente. Mapeia páginas virtuais para regiões do arquivo em disco. Cópia real ocorre sob demanda quando a CPU referencia cada página. Ver [[Memória Virtual]].

## Interposicionamento de biblioteca

Intercepta chamadas a funções de biblioteca e executa código próprio. Útil para rastreamento, validação ou substituição.

| Momento | Mecanismo | Requisito |
|---|---|---|
| **Compilação** | `#define malloc mymalloc` em `malloc.h` local + `-I.` | Código-fonte |
| **Ligação** | `gcc -Wl,--wrap,malloc` → resolve `malloc` como `__wrap_malloc` | Arquivos `.o` |
| **Execução** | `LD_PRELOAD=./mymalloc.so ./prog` | Só o executável |

```bash
# run-time: funciona em qualquer executável sem recompilação
LD_PRELOAD="./mymalloc.so" /usr/bin/uptime
```

No wrapper de run-time, `dlsym(RTLD_NEXT, "malloc")` obtém o endereço da `malloc` real.

## Ferramentas para arquivos objeto

| Ferramenta | Função |
|---|---|
| `ar` | Cria e manipula bibliotecas estáticas (`.a`) |
| `nm` | Lista símbolos na tabela de símbolos |
| `size` | Lista nomes e tamanhos das seções |
| `strings` | Lista strings imprimíveis no arquivo |
| `strip` | Remove tabela de símbolos |
| `readelf` | Estrutura completa do arquivo ELF (engloba `size` e `nm`) |
| `objdump` | Desmonta e exibe todo conteúdo do arquivo objeto |
| `ldd` | Lista bibliotecas compartilhadas necessárias em runtime |

## Ver também
- [[Linguagem Assembly]]
- [[Processo de Montagem]]
- [[Tradução e Interpretação]]
- [[Sistema de Compilação]]
- [[Formato ELF]] — estrutura detalhada de arquivos objeto ELF
- [[PIC e GOT]] — código independente de posição, GOT e PLT
- [[Memória Virtual]]
- [[Paginação x86]]
- [[Sistema Operacional]]
