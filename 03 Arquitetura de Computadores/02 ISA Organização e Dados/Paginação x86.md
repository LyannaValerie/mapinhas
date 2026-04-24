---
title: Paginação x86
aliases:
  - paginação
  - page table
  - tabela de páginas
  - diretório de páginas
  - TLB
  - Translation Look-aside Buffer
  - page fault
  - swap file
  - arquivo de troca
  - endereço linear
  - endereço físico
  - PAE
  - Physical Address Extension
  - PSE
  - PML4
  - PML5
  - PDPT
  - execute disable
  - NX
  - XD
  - large page
  - huge page
tags:
  - computação/arquitetura
date: 2026-04-09
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Paginação x86

## Definição
Sistema que habilita a **[[Memória Virtual]]** no modo protegido e no modo de 64 bits. Divide RAM e arquivo de troca em blocos chamados **páginas**, traduzindo automaticamente endereços lineares (usados por programas) em endereços físicos (localização real na RAM ou no swap).

---

## Motivação

Quando a RAM está cheia, páginas não usadas no momento são salvas no **arquivo de troca (swap file)** em disco. Quando necessárias novamente, outras páginas são movidas para o swap e as solicitadas voltam para a RAM.

> [!warning] Swap é lento
> Armazenamento de massa é muito mais lento que RAM. Cada troca de páginas é perceptível ao usuário. É por isso que **mais RAM deixa o computador mais rápido** — não porque o processador fica mais poderoso, mas porque são necessárias **menos trocas** com o swap.

---

## Modos de Paginação

| Modo | Endereço físico | Endereço linear | Tamanhos de página | Execute Disable |
|---|---|---|---|---|
| **32 bits** | 32 bits (4 GiB) | 32 bits (4 GiB) | 4 KiB | Não |
| **32 bits + PSE** | 32 bits (4 GiB) | 32 bits (4 GiB) | 4 KiB ou **4 MiB** | Não |
| **32 bits + PSE-36** | 36 bits (64 GiB) | 32 bits (4 GiB) | 4 KiB ou 4 MiB | Não |
| **PAE** | Até 52 bits (4 PiB); mais usual: 36 bits (64 GiB) | 32 bits (4 GiB) | 4 KiB ou **2 MiB** | Sim (XD/NX) |
| **4 níveis** (modo 64 bits) | Até 52 bits (4 PiB) | Até **48 bits (256 TiB)** | 4 KiB, 2 MiB ou **1 GiB** | Sim |
| **5 níveis** (Sunny Cove+) | Até 52 bits (4 PiB) | Até **57 bits (128 PiB)** | 4 KiB, 2 MiB ou 1 GiB | Sim |

> [!note] Tamanho de página padrão = 4 KiB
> Todos os sistemas operacionais usam páginas de 4 KiB por padrão. Páginas maiores (2 MiB = "large page" no Windows; 1 GiB) só beneficiam aplicações específicas de alto poder computacional.

> [!note] SO 32 bits e o limite de 4 GiB
> Versões 32 bits do Windows para usuário final usam PAE mas **não permitem > 4 GiB de RAM** visível. O espaço de 4 GiB inclui RAM + arquivo de troca somados. Por isso, com 4 GiB instalados, menos de 4 GiB são reconhecidos (parte do espaço é reservada para o swap e mapeamentos de hardware).

---

## Funcionamento: endereço linear vs. físico

Programas operam sobre **endereços lineares** — eles "pensam" que a memória é sequencial. A **unidade de paginação** do processador traduz cada endereço linear para o **endereço físico** real:

```
Programa → endereço linear → [Unidade de Paginação] → endereço físico → RAM (ou swap)
```

As tabelas de tradução ficam na RAM, com CR3 apontando para o topo da hierarquia. Para evitar o custo de percorrer essas tabelas a cada acesso, o processador mantém o **TLB**.

---

## Hierarquia de Tabelas por Modo

O endereço linear é fatiado em campos, cada um indexando um nível da hierarquia de tabelas. Ao final, os bits menos significativos são o offset dentro da página.

### 32 bits — 4 KiB (2 níveis)
```
[31–22: PD index (10)] [21–12: PT index (10)] [11–0: offset (12)]
CR3 → Page Directory (PD, 1024 PDEs) → Page Table (PT, 1024 PTEs) → Página
```

### PAE — 4 KiB (3 níveis)
Entradas ampliadas de 32 para **64 bits**; tables reduzidas a 512 entradas cada.
```
[31–30: PDPT index (2)] [29–21: PD index (9)] [20–12: PT index (9)] [11–0: offset (12)]
CR3 → PDPT (4 PDPTEs) → Page Directory (512 PDEs) → Page Table (512 PTEs) → Página
```

### 4 níveis — 4 KiB (modo 64 bits)
```
[47–39: PML4 (9)] [38–30: PDPT (9)] [29–21: PD (9)] [20–12: PT (9)] [11–0: offset (12)]
CR3 → PML4 (512) → PDPT (512) → PD (512) → PT (512) → Página
Bits 63–48 do endereço linear não são usados (extensão de sinal)
```

### 5 níveis — 4 KiB (Sunny Cove+)
Adiciona tabela **PML5** antes da PML4:
```
[56–48: PML5 (9)] [47–39: PML4 (9)] [38–30: PDPT (9)] [29–21: PD (9)] [20–12: PT (9)] [11–0: offset (12)]
```

### Páginas maiores (2 MiB / 1 GiB)
Páginas maiores eliminam os níveis de tabela mais internos, pois o offset fica mais longo:
- **2 MiB:** elimina a tabela de páginas (PT); 21 bits de offset
- **1 GiB:** elimina PT e PDPT; 30 bits de offset

---

## Campos das Entradas de Tabela

| Campo | Descrição |
|---|---|
| **Endereço** | Bits mais significativos do endereço físico da próxima tabela ou da página |
| **P** (Present) | 1 = entrada válida e presente |
| **R/W** (Read/Write) | 0 = somente leitura; 1 = leitura e escrita para ring 3 |
| **U/S** (User/Supervisor) | 0 = somente rings 0–2; 1 = qualquer ring |
| **PWT** (Page Write-Through) | Habilita escrita direta no cache (write-through) |
| **PCD** (Page Cache Disable) | Desabilita cache para esta página |
| **A** (Accessed) | Processador marca 1 no primeiro acesso; mantém o valor |
| **D** (Dirty) | 1 = página foi modificada desde a criação |
| **G** (Global) | Página compartilhável entre programas |
| **PAT** | Com PCD+PWT: define política de cache (ver abaixo) |
| **XD/NX** (Execute Disable / No Execute) | 1 = impede execução de código nesta página (PAE e 4/5 níveis) |
| **Chave** | 4 bits (Skylake+, PKE em CR4): índice no registrador PKRU para controle fino de permissões |

### Política de cache (bits PAT + PCD + PWT)

| PAT | PCD | PWT | Política |
|---|---|---|---|
| 0 | 0 | 0 | **UC** — Uncacheable (sem cache) |
| 0 | 0 | 1 | **WC** — Write Combining |
| 0 | 1 | 0 | Reservado |
| 0 | 1 | 1 | Reservado |
| 1 | 0 | 0 | **WT** — Write Through (escrita direta) |
| 1 | 0 | 1 | **WP** — Write Protected |
| 1 | 1 | 0 | **WB** — Write Back (escrita indireta) |

---

## Execute Disable (XD / NX)

Presente no PAE e nos modos de 4 e 5 níveis. Quando ativado em uma página, impede que o processador **execute código armazenado nela**. Útil para prevenir vírus que se instalam em páginas de dados.

- Intel chama de **XD** (eXecute Disable)
- AMD chama de **NX** (No eXecute)

---

## TLB — Translation Look-aside Buffer

Cache interno do processador que armazena as **últimas conversões** endereço linear → endereço físico (incluindo propriedades da página como nível de privilégio).

- A cada conversão feita, o resultado é guardado no TLB
- Se o endereço linear já está no TLB → resultado imediato, sem percorrer as tabelas
- TLB cheio → entradas mais antigas são descartadas; próxima conversão percorre as tabelas novamente
- Tamanho varia por modelo; fabricantes aumentam o TLB a cada nova microarquitetura

> [!tip] TLB e desempenho
> Um TLB maior reduz a frequência de "misses" (conversões que precisam percorrer as tabelas na RAM), aumentando o desempenho geral do sistema. É um dos muitos fatores que diferenciam microarquiteturas.

---

## Ver também
- [[Memória Virtual]] — perspectiva do SO: espaço por processo, page fault, heap/stack
- [[Paginação]] — conceito geral independente de arquitetura
- [[Paginação por Demanda]] — carregamento sob demanda, working set, thrashing
- [[Política de Substituição de Página]] — NRU, FIFO, Second Chance, LRU
- [[Organização de Memória x86]] — endereços lineares vs. físicos, segmentação
- [[Proteção de Memória x86]] — descritores de segmento, rings de privilégio
- [[Registradores x86]] — CR3 (base da tabela de páginas), CR2 (endereço da page fault)
- [[Modos de Operação x86]] — qual modo de paginação é usado em cada modo do processador
- [[Cache]] — TLB é um cache especializado; PAT/PCD/PWT configuram a política de cache L1/L2 por página
