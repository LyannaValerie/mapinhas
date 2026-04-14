---
title: Cache
aliases:
  - memória cache
  - cache L1
  - cache L2
  - cache L3
  - L1
  - L2
  - L3
tags:
  - computação/fundamentos
date: 2026-04-06
---

# Cache

## Definição
Memória de alta velocidade localizada dentro ou próxima do [[Processador]], usada como intermediária entre a **unidade de busca** do processador e a [[Memória|RAM]]. Sem cache, a unidade de busca ficaria ociosa esperando a RAM entregar cada dado solicitado — a RAM é significativamente mais lenta que o processador.

> [!note] Pronúncia
> A pronúncia correta é **"cash"** (como a palavra inglesa para dinheiro), não "cachê". Os fabricantes de processadores abreviam cache com o símbolo **$** — uma brincadeira com a pronúncia idêntica de "cache" e "cash" em inglês.

## Relações (SPO)
- Cache → localizada em → [[Processador]] (ou próxima a ele)
- Cache → armazena → palavras de memória mais frequentemente usadas
- Cache → mais rápida que → [[Memória]] (RAM)
- Cache → mais lenta que → [[Registrador|registradores]]
- Cache → introduzida comercialmente em → Intel 80486 (1989)

## Hierarquia de memória

```
Velocidade (mais rápido → mais lento):
  Registradores  >  Cache L1  >  Cache L2  >  Cache L3  >  RAM  >  Armazenamento

Tamanho (menor → maior):
  Registradores  <  Cache L1  <  Cache L2  <  Cache L3  <  RAM  <  Armazenamento
```

## Níveis de cache

| Nível | Localização | Tamanho típico | Latência | Por núcleo? |
|---|---|---|---|---|
| **L1** | Dentro do chip do processador | 8–64 KB | ~4 ciclos | Sim (separada para instruções e dados) |
| **L2** | Dentro do chip (ou pacote) | 256 KB – 4 MB | ~12 ciclos | Sim |
| **L3** | Dentro do chip | 4–32 MB | ~40 ciclos | **Compartilhada** entre todos os núcleos |

## Histórico na família Intel

- **80486 (1989):** primeira cache embutida no chip — 8 KB L1
- **Pentium Pro (1995):** L1 de 8 KB (instruções) + 8 KB (dados); L2 de 256 KB no mesmo pacote
- **Core i7 (2011+):** L1 e L2 por núcleo; L3 compartilhada; aumenta com o número de núcleos

## Tecnologia: SRAM vs. DRAM

| Tipo | Usado em | Velocidade | Custo | Volatilidade |
|---|---|---|---|---|
| **SRAM** (Static RAM) | Cache L1, L2, L3 | Muito rápida | Alto | Volátil |
| **DRAM** (Dynamic RAM) | [[Memória]] principal (RAM) | ~100× mais lenta que SRAM | Baixo | Volátil |

SRAM mantém o estado enquanto houver energia, sem necessidade de *refresh* — por isso é mais rápida. DRAM precisa ser periodicamente atualizada, o que adiciona latência.

## O gap processador-memória

Uma lei física fundamental:
> **Dispositivos de armazenamento maiores são mais lentos. Dispositivos mais rápidos custam mais por byte.**

Consequência prática (valores típicos):

| Comparação | Diferença de tamanho | Diferença de velocidade |
|---|---|---|
| Disco vs. RAM | Disco ~1.000× maior | Disco ~10.000.000× mais lento |
| RAM vs. registradores | RAM ~bilhões de bytes vs. ~centenas de bytes | RAM ~100× mais lenta |
| L1 vs. L2 | L2 maior | L2 ~5× mais lento que L1 |
| L2 vs. RAM | RAM muito maior | RAM 5–10× mais lenta que L2 |

> [!warning] O gap está crescendo
> Com o avanço da tecnologia de semicondutores, é mais fácil e barato tornar processadores mais rápidos do que tornar a memória principal mais rápida. O **gap processador-memória aumenta ao longo do tempo** — tornando o papel das caches cada vez mais crítico.

## Princípio de funcionamento — Localidade

O processador sempre consulta a cache antes da RAM. Se a palavra estiver na cache (**cache hit**), o acesso é muito mais rápido. Se não estiver (**cache miss**), acessa a RAM e carrega o bloco para a cache.

> [!tip] Por que funciona? — Princípio da localidade
> Programas tendem a:
> - **Localidade temporal:** reusar as mesmas instruções e dados repetidamente (ex: laços)
> - **Localidade espacial:** acessar posições de memória próximas umas das outras (ex: arrays sequenciais)
>
> Caches exploram esses padrões para manter na memória rápida exatamente o que o programa usará em breve. Programadores que conhecem caches podem estruturar seu código para explorar localidade — obtendo ganhos de desempenho de **uma ordem de grandeza**.

## Hit, Miss e Taxa de Acerto

- **Hit:** dado encontrado no cache → sem acesso à RAM
- **Miss:** dado ausente → busca na RAM + carrega linha inteira no cache
- **Taxa de acerto (hit rate h):** 80–99% na prática

### Fórmula do Tempo de Acesso Médio

Sejam:
- **c** = tempo de acesso ao cache
- **m** = tempo de acesso à memória principal
- **h** = taxa de acerto (hit rate)

$\text{tempo de acesso médio} = c + (1 - h) \cdot m$

| Caso limite | Resultado |
|---|---|
| h → 1 (tudo no cache) | tempo → c |
| h → 0 (nada no cache) | tempo → c + m (verifica cache + acessa memória) |

> [!example] Exemplo numérico
> c = 2 ns, m = 20 ns, h = 0,95 → tempo médio = 2 + (0,05 × 20) = **3 ns** (vs. 20 ns sem cache)

### Linha de Cache (Cache Line)

Quando ocorre um miss, não se busca apenas a palavra solicitada — busca-se um **bloco contíguo** denominado **linha de cache** (tipicamente 64 bytes em CPUs modernas).

Motivação: explorar **localidade espacial** — palavras adjacentes na memória provavelmente serão acessadas em breve. Buscar k palavras de uma vez é mais eficiente do que k buscas individuais (o barramento pode transferir 64 ou 128 bits por ciclo).

## Política de Escrita

| Política | Comportamento | Desempenho |
|----------|--------------|------------|
| **Write-through** (escrita direta) | RAM atualizada imediatamente junto com o cache | Mais lento (acessa RAM a cada write) |
| **Write-back** (escrita indireta) | RAM atualizada só quando a linha é descartada do cache | Mais rápido — método atual |

Estados de linha no write-back: **MESI** (Modified, Exclusive, Shared, Invalid).  
No write-through: apenas Shared e Invalid.

### Coerência e Snooping

Com write-back, o cache pode ter dados mais recentes que a RAM. Dispositivos que acessam a RAM diretamente (DMA) precisam checar com o processador antes — procedimento chamado **bisbilhotagem (snooping)** — para evitar corrupção de dados.

## Arquiteturas de Cache

### Cache com Mapeamento Direto
- Mais simples. RAM dividida em blocos do tamanho do cache.
- Cada linha do cache mapeia exclusivamente a linha de mesmo número em qualquer bloco.
- **Problema:** dois dados de linhas iguais em blocos diferentes não podem coexistir no cache.
- Diretório simples: guarda apenas qual bloco está em cada linha.

### Cache Completamente Associativo
- Qualquer linha da RAM pode ir para qualquer linha do cache.
- Elimina colisões, mas o diretório é enorme e a verificação lenta.
- **Paradoxo:** pode ser mais lento buscar no cache do que ir direto à RAM.

### Cache Associativo por Grupos *(mais usado)*
- Divide o cache em N vias independentes (ex: 4-way = 4 × 64 KiB num cache de 256 KiB).
- Cada via funciona como mapeamento direto, mas até N linhas de mesmo número podem coexistir.
- Equilibra simplicidade de diretório com flexibilidade de armazenamento.

## Cache L1 Dividido vs. Unificado

| Modelo | Descrição |
|--------|-----------|
| **Unificado** | Instruções e dados no mesmo cache (primeiros processadores) |
| **Dividido** (atual) | L1-I (instruções) + L1-D (dados) separados |

L1-I: cache "de entrada" (instruções + operandos). L1-D: cache "de saída" (load/store).

## Histórico de Localização

| Era | Onde estava o cache |
|-----|---------------------|
| 386DX | Na placa-mãe (externo) |
| 486 | Pequena porção dentro do chip + externo na placa |
| Pentium Pro / Pentium II (Intel), Athlon (AMD) | Todo dentro do processador (atual) |

## Endereçamento no Mapeamento Direto — Detalhamento

Para cache com mapeamento direto de **2048 linhas**, cada linha com **4 palavras de 4 bytes** (16 bytes/linha), sobre endereços de 32 bits:

```
31        16 15       5 4   2 1 0
|  TAG(16)  | LINE(11) | WORD(3) | BYTE(2) |
```

| Campo | Bits | Uso |
|---|---|---|
| **BYTE** | 2 | offset dentro da palavra (bytes individuais) |
| **WORD** | varia | qual palavra dentro da linha |
| **LINE** | 11 | qual linha do cache (0–2047) |
| **TAG** | restante | identifica qual bloco de RAM está nessa linha |

### Estrutura de Cada Entrada do Cache

```
Entrada i:
[ valid (1 bit) | tag (16 bits) | data (linha inteira) ]
```

- **valid**: 1 = entrada contém dado válido; 0 = linha vázia/inválida (inicialização ou inv.álida por coerência).
- **tag**: compara com os bits TAG do endereço. Se `valid=1` e `tag` bate → **hit**; caso contrário → **miss**.
- **data**: conteúdo da linha (todos os bytes/palavras).

## Algoritmo LRU (Least Recently Used)

Usado em caches n-way set-associative para escolher qual via despejar no miss:

1. Manter contador ou ordem de uso por via dentro do conjunto.
2. No acesso: marcar via acessada como MRU (most recently used).
3. No despejo: remover a via LRU (menos recentemente usada).

Para 2-way: basta 1 bit por conjunto (0 = via 0 é LRU; 1 = via 1 é LRU).
Para n-way maior: implementações práticas usam **pseudo-LRU** (aproximação binoméria com árvore de bits) para reduzir overhead de hardware.

## Política Write-Allocate

Complementa a política de escrita:

| Combinação típica | Comportamento no miss de escrita |
|---|---|
| **Write-back + write-allocate** | Carrega linha para cache, aplica escrita na cache; escreve na RAM só no despejo |
| **Write-through + no-write-allocate** | Escreve direto na RAM sem carregar linha para cache |

Write-allocate é preferível quando há localidade temporal na escrita (padrão mais comum).

## Ver também
- [[Desempenho do Processador]] — cache como uma das principais tecnologias de desempenho
- [[Hierarquia de Memória]] — posicionamento da cache na hierarquia completa L0–L6
- [[Processador]]
- [[Memória]]
- [[Registrador]]
- [[Intel x86]]
- [[Níveis de Abstração]]
