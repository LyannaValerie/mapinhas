---
title: Paginação
aliases:
  - paging
  - página
  - quadro de página
  - tabela de páginas
tags:
  - computação/arquitetura
  - hardware
  - memória
date: 2026-04-21
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Paginação

## Definição

Técnica de [[Memória Virtual]] que divide o espaço de endereço virtual em blocos de tamanho fixo chamados **páginas** e a memória física em blocos do mesmo tamanho chamados **quadros de página** (page frames). O hardware de MMU mapeia cada página virtual para um quadro físico via [[#Tabela de páginas]].

Contrasta com [[Segmentação]], que usa blocos de tamanho variável.

## Relações (SPO)

| Sujeito | Predicado | Objeto |
|---|---|---|
| Paginação | é mecanismo de | [[Memória Virtual]] |
| MMU | traduz | endereço virtual → endereço físico |
| Tabela de páginas | mapeia | página virtual → quadro de página |
| Bit presente/ausente | indica | página está na RAM ou no disco |
| Falta de página | dispara | carregamento de página do disco |
| Tamanho de página | é sempre | potência de 2 |

## Estrutura do endereço

Um endereço virtual com tamanho de página $2^k$ é dividido em:

```
[ número da página virtual | deslocamento (offset) ]
←      n - k bits        →←       k bits          →
```

O **número da página** indexa a tabela de páginas; o **deslocamento** é copiado diretamente para o endereço físico.

Exemplo — endereço de 32 bits, página de 4 KB ($k = 12$):
- 20 bits para número da página → até 1M páginas virtuais
- 12 bits de deslocamento → 4096 bytes por página

## Tabela de páginas

Estrutura mantida pelo SO, uma por processo, que armazena o mapeamento página virtual → quadro de página.

Cada **entrada da tabela de páginas** contém:

| Campo | Função |
|---|---|
| Número do quadro de página | Endereço físico base do quadro |
| Bit P (presente/ausente) | 1 = página na RAM, 0 = no disco |
| Bit M (modificado / dirty) | 1 = página foi escrita desde o último carregamento |
| Bit R (referenciado) | 1 = página foi acessada (usado por [[Política de Substituição de Página]]) |
| Bits de proteção | leitura / escrita / execução permitidos |

### Falta de página (page fault)

Quando P = 0, a MMU gera uma **falta de página** (trap ao SO):
1. SO escolhe um quadro de página para liberar (ver [[Política de Substituição de Página]])
2. Se o quadro estava sujo (M = 1), grava no disco
3. Carrega a página requerida do disco para o quadro
4. Atualiza a entrada na tabela de páginas (P = 1, número do quadro)
5. Reinicia a instrução que causou a falha

### Tabelas multi-nível

Para espaços de 32 ou 64 bits, uma tabela flat seria enorme. Tabelas multi-nível (2, 3 ou 4 níveis) resolvem isso: apenas as partes do espaço de endereço em uso têm subtabelas alocadas.

> [!example] Core i7 (32 bits legacy)
> Usa dois níveis: diretório de páginas (DIR, 10 bits) + tabela de páginas (PAGE, 10 bits) + deslocamento (OFF, 12 bits). Ver [[Paginação x86]].

## TLB — aceleração de tradução

A tabela de páginas fica na memória → cada acesso levaria 2 acessos à memória. O hardware mantém uma cache de traduções chamada **TLB** (Translation Look-aside Buffer):

- Acerto de TLB: tradução em ~1 ciclo
- Falta de TLB: MMU percorre a tabela de páginas na memória (page walk)

Ver detalhes arquitetura-específicos em [[Paginação x86]].

## Tamanho de página

| Tamanho menor | Tamanho maior |
|---|---|
| Fragmentação interna menor | Tabela de páginas menor |
| Tabela de páginas maior | Menos faltas de página |
| Mais faltas de página (menos dados carregados por falta) | Fragmentação interna maior |

Tamanhos comuns: 4 KB (padrão x86/ARM), 2 MB / 1 GB (huge pages x86).

## Comparação: paginação × segmentação

| Consideração | Paginação | [[Segmentação]] |
|---|---|---|
| Programador precisa estar ciente? | Não | Sim |
| Nº de espaços de endereço lineares | 1 | Muitos |
| Espaço virtual > memória física? | Sim | Sim |
| Tabelas de tamanho variável | Difícil | Fácil |
| Proteção por tipo de objeto | Difícil | Fácil |
| Fragmentação | Interna | Externa |

## Ver também

- [[Memória Virtual]] — abstração geral
- [[Paginação x86]] — implementação específica do x86
- [[Paginação por Demanda]] — páginas carregadas só quando necessário
- [[Política de Substituição de Página]] — NRU, FIFO, LRU
- [[Segmentação]] — alternativa com blocos de tamanho variável
- [[Cache]] — analogia com hierarquia de memória
