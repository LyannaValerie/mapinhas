---
title: PIC e GOT
aliases:
  - PIC
  - position-independent code
  - código independente de posição
  - GOT
  - global offset table
  - tabela de deslocamento global
  - PLT
  - procedure linkage table
  - tabela de ligação de procedimentos
  - lazy binding
  - ligação preguiçosa
tags:
  - computação/arquitetura
date: 2026-04-14
---

# PIC e GOT

## Definição
**Position-Independent Code (PIC)** — código que pode ser carregado em qualquer endereço de memória sem modificação pelo [[Ligador]]. Requisito para bibliotecas compartilhadas (`.so`): múltiplos processos precisam compartilhar uma única cópia do segmento de código.

## Relações (SPO)
- PIC → permite → carregar biblioteca em qualquer endereço sem relocação do código
- GOT → armazena → endereços absolutos de globais (preenchidos em load time)
- PLT → implementa → lazy binding de chamadas a funções externas
- Lazy binding → adia → resolução de endereço até primeira chamada
- `-fpic` → instrui → gcc a gerar PIC
- `-shared` → instrui → ligador a produzir `.so`

## Por que PIC é necessário

Sem PIC, bibliotecas precisariam de endereço fixo pré-acordado — conflitos inevitáveis entre centenas de bibliotecas. Relocar ao carregar exigiria modificar o segmento de código, impossibilitando o compartilhamento entre processos (o segmento de código seria privado por processo).

**Princípio fundamental**: a distância entre o segmento de código e o segmento de dados é **constante em runtime**, independente do endereço de carga. Isso permite referenciar dados via offset fixo relativo ao PC.

## GOT — referências a dados globais

O compilador cria uma **Global Offset Table (GOT)** no início do segmento de dados. Cada variável global referenciada tem uma entrada de 8 bytes. O ligador dinâmico preenche as entradas com endereços absolutos no load time.

```
Segmento de dados
┌────────────────────────┐
│ GOT[0]  addr .dynamic  │
│ GOT[1]  addr reloc entries │
│ GOT[2]  addr ligador dinâmico │
│ GOT[3]  → &addcnt      │  ← preenchido pelo ligador dinâmico
│ ...                    │
└────────────────────────┘
```

Código PIC que acessa `addcnt`:
```asm
mov  0x2008b9(%rip), %rax   # %rax = GOT[3] (endereço de addcnt)
addl $0x1, (%rax)            # addcnt++
```

O offset `0x2008b9` é a distância fixa (compile-time) entre essa instrução e `GOT[3]`. Não depende do endereço de carga — é sempre o mesmo.

## PLT — chamadas a funções externas (lazy binding)

Chamadas a funções de `.so` passam pela **Procedure Linkage Table (PLT)**, implementando *lazy binding*: o endereço real é resolvido apenas na **primeira** chamada.

### Estrutura da PLT

```
Segmento de código (PLT)
┌──────────────────────────────────┐
│ PLT[0]  chama o ligador dinâmico │
│ PLT[1]  __libc_start_main        │
│ PLT[2]  addvec                   │  uma entrada de 16 bytes por função
│ PLT[3]  printf                   │
└──────────────────────────────────┘
```

Código de cada entrada PLT[k]:
```asm
PLT[2]:
    jmpq  *GOT[4]     # salta para endereço em GOT[4]
    pushq $0x1        # ID da função (addvec = 1)
    jmpq  PLT[0]      # invoca ligador dinâmico
```

Inicialmente, `GOT[4]` aponta para a instrução `pushq` de PLT[2] (segunda instrução). Isso cria um loop proposital que termina chamando o ligador dinâmico.

### Primeira chamada

```
1. callq PLT[2]
2. jmpq *GOT[4]  →  GOT[4] = addr de PLT[2]+6 (pushq)
3. pushq $0x1    →  empilha ID de addvec
4. jmpq PLT[0]   →  invoca ligador dinâmico
5. Ligador resolve addvec, escreve endereço real em GOT[4]
6. Execução em addvec
```

### Chamadas subsequentes

```
1. callq PLT[2]
2. jmpq *GOT[4]  →  GOT[4] = endereço real de addvec
3. Execução direta em addvec  (custo: 1 instrução + 1 acesso à memória)
```

Lazy binding evita centenas ou milhares de relocações de carga desnecessárias — a maioria das funções de uma biblioteca nunca é chamada por um programa específico.

## Compilando com PIC

```bash
# Biblioteca compartilhada: -fpic obrigatório
gcc -shared -fpic -o libvector.so addvec.c multvec.c

# Executável que usa a lib (ligação em load time)
gcc -o prog main.c ./libvector.so

# Executável com dlopen (ligação explícita em run time)
gcc -rdynamic -o prog dll.c -ldl
```

## API de ligação dinâmica explícita (`dlfcn.h`)

```c
#include <dlfcn.h>

// Carrega .so; flag: RTLD_NOW (resolve imediatamente) ou RTLD_LAZY
void *handle = dlopen("./libvector.so", RTLD_LAZY);

// Obtém ponteiro para símbolo
void (*addvec)(int*, int*, int*, int) = dlsym(handle, "addvec");

// Verifica erro
const char *err = dlerror();  // NULL se OK

// Descarrega (se nenhum outro usuário)
dlclose(handle);
```

Flags adicionais para `dlopen`:
- `RTLD_GLOBAL`: símbolos da lib ficam disponíveis para resolução por libs carregadas depois

## LD_PRELOAD — interposicionamento em runtime

Variável de ambiente que força o ligador dinâmico a carregar uma lib antes de qualquer outra, permitindo substituir funções de qualquer `.so` (incluindo `libc.so`):

```bash
LD_PRELOAD="./mymalloc.so" ./prog
LD_PRELOAD="./mymalloc.so" /usr/bin/uptime   # funciona em qualquer executável
```

No wrapper, use `dlsym(RTLD_NEXT, "malloc")` para obter o endereço da `malloc` real (próxima na ordem de busca).

Ver [[Ligador#Interposicionamento de biblioteca]] para os três momentos de interposicionamento.

## Ver também
- [[Ligador]] — contexto geral de ligação dinâmica e tipos de símbolos
- [[Formato ELF]] — estrutura do arquivo onde GOT (`.data`) e PLT (`.text`) residem
- [[Memória Virtual]] — como o segmento de código PIC é compartilhado entre processos
- [[Sistema de Compilação]] — flags de compilação para PIC
- [[Proteção de Memória x86]] — permissões r-x / rw- nos segmentos
