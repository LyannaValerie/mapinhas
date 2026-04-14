---
title: Memória Virtual
aliases:
  - virtual memory
  - espaço de endereçamento virtual
  - virtual address space
  - heap
  - pilha de programa
  - stack
  - endereço virtual
  - VA
  - endereço físico
  - PA
  - página virtual
  - VP
  - página física
  - PP
  - page hit
  - page fault
  - falta de página
  - tabela de páginas
  - page table
  - PTE
  - VPN
  - VPO
  - PPN
  - PPO
tags:
  - computação/arquitetura
date: 2026-04-06
---

# Memória Virtual

## Definição
Abstração do [[Sistema Operacional]] que fornece a cada [[Processo]] a ilusão de que possui **uso exclusivo de toda a memória principal**. Implementada pela combinação de hardware de tradução de endereços (MMU), tabelas de páginas mantidas pelo SO, e tratamento de exceções (page fault).

Três capacidades fundamentais:
1. Usa DRAM eficientemente como cache para espaço de endereçamento armazenado em disco
2. Simplifica gerenciamento de memória fornecendo espaço uniforme a cada processo
3. Protege o espaço de endereçamento de cada processo contra corrupção por outros processos

## Relações (SPO)
- Memória Virtual → fornece → ilusão de memória exclusiva para cada [[Processo]]
- Memória Virtual → usa → [[Hierarquia de Memória|DRAM como cache]] para o disco
- Memória Virtual → implementada por → MMU (hardware) + [[Sistema Operacional]] (tabela de páginas)
- Memória Virtual → fornece → gerenciamento e proteção de memória por processo
- MMU → traduz → VA → PA usando tabela de páginas
- [[Processo]] → possui → tabela de páginas própria no SO

---

## Endereçamento Físico e Virtual

**Endereçamento físico:** CPU gera diretamente PA (physical address). Usado em DSPs, microcontroladores, supercomputadores Cray.

**Endereçamento virtual:** CPU gera VA (virtual address) → MMU traduz para PA antes de enviar ao barramento. Padrão em processadores modernos.

```
CPU → VA → [MMU] → PA → RAM
```

A MMU realiza tradução usando tabela de páginas na RAM. Endereço base da tabela mantido em registrador especial (CR3 no x86-64).

---

## VM como Cache (DRAM Cache)

Espaço virtual: N = 2ⁿ bytes. Memória física: M = 2ᵐ bytes (N >> M tipicamente).

### Páginas virtuais e físicas

Espaço virtual dividido em **páginas virtuais (VP)** de tamanho P = 2ᵖ bytes. DRAM dividida em **páginas físicas (PP)** (*frames*) de mesmo tamanho P.

Estados de cada VP:

| Estado | Descrição |
|---|---|
| **Não alocada** | Nunca foi criada; não ocupa espaço em disco |
| **Cached** | Mapeada e presente na DRAM |
| **Uncached** | Alocada no disco, ausente da DRAM |

### Tabela de páginas

Estrutura na RAM mantida pelo SO. Mapeia VPs → PPs. Cada entrada (**PTE — Page Table Entry**) contém:
- **Bit de validade (P):** VP está na DRAM?
- **Endereço:** PA da PP (se P=1) ou endereço no disco (se P=0)
- **Bits de proteção:** leitura, escrita, execução, privilégio

### Page Hit

VA referencia VP presente na DRAM (PTE com P=1). MMU traduz diretamente. Sem custo extra.

### Page Fault

VA referencia VP ausente da DRAM (PTE com P=0). Exceção disparada:

1. MMU dispara exceção → kernel assume controle
2. Kernel seleciona página vítima (algoritmo de substituição)
3. Se vítima foi modificada (*dirty*), kernel salva no disco
4. Kernel carrega VP solicitada do disco para a PP vítima
5. Kernel atualiza PTE; retorna ao processo
6. Instrução re-executada → agora page hit

> [!warning] Custo de page fault
> Disco é ~100.000× mais lento que DRAM. Thrashing (page faults frequentes) degrada severamente o desempenho — o working set do processo não cabe na DRAM disponível.

---

## O espaço de endereçamento virtual (Linux/x86-64)

```
Endereço alto  ┌─────────────────────────────┐
               │  Memória virtual do kernel  │  ← invisível ao código de usuário
               ├─────────────────────────────┤
               │  Pilha de usuário (stack)   │  ↕ cresce/diminui em tempo de execução
               ├─────────────────────────────┤
               │  Bibliotecas compartilhadas │  ex: libc.so, libm.so
               ├─────────────────────────────┤
               │  Heap                       │  ↕ cresce/diminui (malloc/free)
               ├─────────────────────────────┤
               │  Dados de leitura/escrita   │  variáveis globais C
               ├─────────────────────────────┤
               │  Código e dados (read-only) │  ← carregado do arquivo executável
Endereço 0     └─────────────────────────────┘
```

| Região | Conteúdo | Tamanho |
|---|---|---|
| **Código e dados** | Instruções + variáveis globais C; carregados do executável | Fixo ao iniciar |
| **Heap** | Memória alocada dinamicamente (`malloc`/`free`) | Cresce/diminui dinamicamente |
| **Bibliotecas compartilhadas** | Código/dados de bibliotecas como `libc` e `libm` | Variável |
| **Pilha (stack)** | Frames de função; cresce a cada chamada, diminui a cada retorno | Cresce/diminui dinamicamente |
| **Memória virtual do kernel** | Código e dados do kernel; inacessível ao código de usuário | Reservado |

> [!note] Endereço fixo do código
> Todos os processos começam com código no mesmo endereço virtual fixo. Facilita vinculação e carregamento pelo [[Sistema de Compilação|linker]].

---

## VM como Ferramenta de Gerenciamento de Memória

Cada processo tem tabela de páginas própria → espaço de endereçamento privado e independente.

**Vinculação simplificada:** processos usam o mesmo layout virtual fixo. O [[Sistema de Compilação|linker]] gera executáveis com endereços virtuais fixos independentemente da posição física.

**Carregamento simplificado:** loader cria PTEs apontando para o arquivo executável sem copiar nada. Páginas carregadas sob demanda (*demand paging*).

**Compartilhamento simplificado:** múltiplos processos mapeiam a mesma PP (ex: código de `libc.so`). Cada um enxerga como parte do próprio espaço virtual.

**Alocação simplificada:** `malloc` obtém VPs contíguas — o SO mapeia para PPs físicas arbitrárias (não precisam ser contíguas).

---

## VM como Ferramenta de Proteção de Memória

Cada PTE contém bits de permissão checados pela MMU a cada acesso:

| Bit | Significado |
|---|---|
| **SUP** | Restrito ao modo supervisor (kernel) |
| **READ** | Leitura permitida |
| **WRITE** | Escrita permitida |
| **EXEC** | Execução permitida |

Violação → exceção → kernel encerra processo (segmentation fault) ou trata.

Como cada processo tem tabela de páginas própria, não pode acessar memória de outro mesmo tentando endereços arbitrários — opera exclusivamente em endereços virtuais do próprio espaço.

---

## Tradução de Endereços

### Estrutura dos endereços

```
VA:  [ VPN  |  VPO ]       PA:  [ PPN  |  PPO ]
      n-p bits  p bits            m-p bits  p bits
```

- **VPN** (Virtual Page Number): índice da VP na tabela de páginas
- **VPO** (Virtual Page Offset): offset dentro da página
- **PPN** (Physical Page Number): obtido da PTE
- **PPO** = VPO (offset não muda na tradução)

### Processo de tradução

1. CPU extrai VPN do VA
2. MMU usa VPN como índice na tabela de páginas (base em CR3 no x86-64)
3. Lê PTE → verifica P:
   - P=1: extrai PPN, concatena com VPO → PA → acessa RAM
   - P=0: dispara page fault
4. MMU envia PA ao barramento de memória

### TLB (Translation Look-aside Buffer)

Cache na MMU para últimas traduções VPN→PPN. Evita acesso à RAM por tradução.

```
VA → [ TLB hit? ] ──→ PPN → PA
           │ miss
           ↓
     tabela de páginas na RAM → PPN → atualiza TLB → PA
```

VA decomposto para consulta TLB:
```
VA:  [ TLBT | TLBI | VPO ]
       tag   index  offset
```

### Tabelas multi-nível

Problema: tabela plana para espaço de 64 bits = 2⁵² PTEs × 8 bytes = 32 PiB.

Solução: VPN dividido em k índices. Cada nível aponta para o próximo. Regiões não alocadas não precisam de sub-tabelas → memória proporcional ao espaço *usado*, não ao *endereçável*.

> [!info] x86-64 usa 4 níveis (PML4/PDPT/PD/PT)
> Ver [[Paginação x86]] para detalhes completos: estrutura de campos, PAE, tamanhos de página, bits XD/NX, CR3.

---

## Ver também
- [[Processo]] — cada processo tem espaço de endereçamento virtual próprio
- [[Sistema Operacional]] — VM é uma das três abstrações fundamentais do SO
- [[Hierarquia de Memória]] — VM estende a hierarquia até o disco
- [[Paginação x86]] — hardware x86: PML4/PDPT/PD/PT, TLB, PAE, XD/NX
- [[Mapeamento de Memória]] — `mmap()`, copy-on-write, `fork`, `execve`, objetos compartilhados
- [[Alocação Dinâmica de Memória]] — `malloc`/`free` internos, fragmentação, listas livres, GC, bugs
- [[Proteção de Memória x86]] — rings, GDT/LDT, descritores de segmento
- [[Sistema de Compilação]] — código/dados carregados do executável para regiões fixas
- [[Organização de Memória x86]] — segmentação, endereçamento linear/segmentado
- [[Modos de Operação x86]] — modos de paginação por modo de operação
