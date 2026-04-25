---
title: Gerenciamento de Memória no Linux
aliases:
  - memória no Linux
  - virtual memory Linux
  - paginação no Linux
  - BSS Linux
tags:
  - computação/sistemas
source: "Sistemas Operacionais Modernos, cap. 10"
created_by: Codex
---

# Gerenciamento de Memória no Linux

## Definição

Subsistema do núcleo Linux que implementa memória virtual, paginação, mapeamentos de arquivos, alocação de páginas físicas e substituição de páginas.

## Papel técnico

Linux dá a cada processo uma visão privada de memória virtual. O núcleo mantém tabelas de páginas, decide permissões de acesso, trata falhas de página e compartilha páginas quando possível (código de bibliotecas, arquivos mapeados).

## Relações (SPO)

- Memória virtual Linux -> usa -> [[Tabela de Páginas]]
- [[MMU]] -> traduz -> endereço virtual para físico
- Falha de página -> aciona -> núcleo Linux
- `mmap` -> cria -> mapeamento de memória
- Copy-on-write -> adia -> cópia após `fork`
- Paginação -> move -> páginas entre memória e armazenamento
- OOM Killer -> mata -> processo de maior "maldade" quando memória crítica

## Espaço de Endereçamento do Processo

Todo processo tem 3 segmentos:

```
┌─────────────────────┐ ← topo
│        Pilha        │  cresce para baixo (frames de chamada)
│         ↓           │
│     (livre)         │
│         ↑           │
│   BSS (não-init.)   │  zerado na carga; não no executável
│   Dados inicializ.  │  valores do compilador
│       Texto         │  instruções; somente leitura, imutável
└─────────────────────┘ ← 0
```

### Segmento de Texto

Instruções de máquina produzidas pelo compilador. Somente leitura; não cresce nem encolhe.

### Segmento de Dados

- **Dados inicializados**: variáveis com valor inicial explícito
- **BSS** (*Block Started by Symbol*): variáveis globais não inicializadas; zeradas ao carregar (mas não armazenadas no executável → economia de espaço)

Exemplo: texto (8 KB) + dados inicializados (8 KB) + cabeçalho = 16 KB no disco. BSS (4 KB) não vai ao disco; o cabeçalho registra apenas o tamanho para o SO alocar e zerar.

## Copy-on-Write (CoW)

No `fork`, filho não recebe cópia física imediata. Ambos compartilham páginas físicas (somente leitura). Ao tentar escrever → falha de página → cópia física criada só para quem escreveu.

**Página zero estática**: Linux aloca durante a inicialização uma página protegida de escrita, cheia de zeros. A região BSS de cada processo aponta para ela. Escrita → CoW → página real alocada.

## Chamadas de Sistema para Memória

| Chamada | Descrição |
|---------|-----------|
| `brk(addr)` | Define o fim do segmento de dados (heap) |
| `mmap(addr, len, prot, flags, fd, off)` | Mapeia arquivo ou memória anônima |
| `munmap(addr, len)` | Desmapeia região |
| `mprotect(addr, len, prot)` | Altera permissões de região |

`malloc` usa `brk` (pequenas alocações) e `mmap` (grandes alocações) internamente.

## VMAs — Virtual Memory Areas

O espaço de endereçamento é descrito por **VMAs**: lista de regiões contíguas com atributos homogêneos (proteção, arquivo mapeado, etc.). Permite:
- Mapeamento de arquivos em memória (`mmap`)
- Compartilhamento de bibliotecas entre processos
- Crescimento dinâmico de pilha (page fault → VMA expandida)

## Paginação

Linux usa paginação de múltiplos níveis (4 níveis em x86-64: PGD, PUD, PMD, PTE). Algoritmo de substituição: variante de **clock** (aproximação de LRU).

- Páginas classificadas como **ativas** ou **inativas**
- Daemon `kswapd` libera páginas inativas quando memória escasseia

## OOM Killer

Quando memória extremamente baixa: **OOM Killer** seleciona processo com maior "maldade" calculada (considera tamanho, tempo, prioridade) e o mata para recuperar RAM.

## Ver também

- [[Gerenciamento de Memória]]
- [[Paginação em Sistemas Operacionais]]
- [[Falha de Página]]
- [[Processos no Linux]]
- [[TLB]]
- [[Memória Virtual]]
