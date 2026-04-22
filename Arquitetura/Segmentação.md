---
title: Segmentação
aliases:
  - segmentação de memória
  - segmento
  - endereço bidimensional
  - fragmentação externa
  - compactação de memória
  - MULTICS
  - melhor ajuste
  - primeiro ajuste
tags:
  - computação/arquitetura
date: 2026-04-13
---

# Segmentação

## Definição
Técnica de organização da memória virtual que fornece ao programador **múltiplos espaços de endereço independentes** (segmentos), ao contrário da [[Memória Virtual|paginação]] que oferece um único espaço linear. Cada segmento é uma sequência linear de endereços com tamanho variável, identificado por um par (número de segmento, deslocamento).

## Relações (SPO)
- Segmentação → fornece → múltiplos espaços de endereço independentes
- Segmento → identificado por → par (número de segmento, deslocamento)
- Segmentação → contrasta com → [[Memória Virtual|paginação]] (espaço único linear)
- Segmentação → causa → fragmentação externa
- Segmentação → elimina → fragmentação interna (tamanho variável)
- MULTICS → combina → segmentação + paginação (primeiro SO a fazer isso)
- Core i7 → implementa → segmentação via LDT/GDT ([[Proteção de Memória x86]])

---

## Problema do espaço de endereço linear

Em uma memória unidimensional, todas as estruturas de dados competem pelo mesmo espaço linear. Exemplo: um compilador mantém 5 tabelas simultaneamente:

1. Tabela de símbolos (cresce continuamente)
2. Texto-fonte salvo para listagem
3. Tabela de constantes
4. Árvore de análise (parse tree)
5. Pilha de chamadas do compilador (cresce e encolhe)

**Problema:** se a tabela de símbolos lotar seu bloco alocado, a compilação falha — mesmo que existam megabytes livres nas outras regiões.

**Solução:** segmentação — cada tabela ocupa um segmento independente que cresce sem interferir nas outras.

---

## Segmentos

Um segmento consiste em uma sequência linear de endereços **de 0 até algum máximo variável**. Comprimentos mudam durante a execução — uma pilha encolhe quando retorna, uma tabela cresce ao ser preenchida.

Para endereçar memória segmentada, o programa especifica **duas partes**:
```
endereço = (número de segmento, deslocamento)
```

Cada segmento contém um único tipo de objeto (procedimento, vetor, pilha etc.) — nunca uma mistura.

---

## Vantagens da segmentação

| Vantagem | Explicação |
|---|---|
| **Tabelas crescentes** | Cada tabela expande no próprio segmento sem conflito com outros |
| **Recompilação independente** | Alterar um procedimento não muda endereços de outros segmentos |
| **Compartilhamento** | Segmentos de biblioteca compartilhados entre processos — uma única cópia física |
| **Proteção por tipo** | Código: só execução. Vetor: leitura/escrita. Proteção aplicada a todo o segmento |

> [!note] Por que proteção faz sentido em segmentação mas não em paginação?
> Em uma memória paginada, as fronteiras de página cortam o programa arbitrariamente — não há como saber se uma página contém código, dados ou pilha. Em um segmento, o conteúdo é uniforme — a proteção adequada ao tipo é aplicada a todo ele.

---

## Fragmentação externa

Segmentos têm tamanho variável. Quando segmentos vêm e vão da memória, surgem **lacunas** entre eles. Esse fenômeno é a **fragmentação externa** (tabuleiro de xadrez):

```
[Seg 0] [lacuna 3K] [Seg 2] [lacuna 1K] [Seg 3] [lacuna 4K] [Seg 5]
         Total livre: 8K, mas nenhuma lacuna contígua comporta Seg 7 de 6K
```

Diferença da fragmentação interna (desperdício dentro de uma página): aqui o espaço desperdiçado está **entre** segmentos.

### Compactação

Solução: mover segmentos para eliminar lacunas, criando uma grande lacuna ao final.

> [!warning] Custo
> Compactar após cada lacuna criada é proibitivo em tempo de CPU. Na prática, compacta-se somente quando o desperdício ultrapassa um limiar (ex: > 20% da memória em lacunas).

### Reunião de lacunas
Quando um segmento é removido, lacunas vizinhas são fundidas em uma só. Exemplo: remover segmento de 4 KB entre dois buracos de 3 KB e 4 KB cria lacuna única de 11 KB.

---

## Algoritmos de alocação de lacuna

### Melhor ajuste (Best Fit)
Escolhe a **menor lacuna que comporta** o segmento. Evita desperdiçar lacunas grandes para segmentos pequenos.

**Problema:** tende a gerar muitas lacunas pequenas e inúteis.

### Primeiro ajuste (First Fit)
Percorre a lista em círculo e escolhe a **primeira lacuna suficientemente grande**.

- Mais rápido (não precisa verificar a lista inteira)
- **Melhor desempenho global** que o melhor ajuste — o melhor ajuste gera fragmentos minúsculos que o primeiro ajuste aproveitaria de outra forma

---

## Implementação: swapping vs. paginação segmentada

| Esquema | Mecanismo | Consequência |
|---|---|---|
| **Swapping** | Segmentos inteiros trocam entre memória e disco | Causa fragmentação externa |
| **Paginação segmentada** | Cada segmento é dividido em páginas de tamanho fixo | Elimina fragmentação externa; cada segmento tem sua tabela de páginas |

Na paginação segmentada, algumas páginas de um segmento podem estar na memória e outras no disco — a única novidade é que cada segmento tem sua própria tabela de páginas.

---

## MULTICS: combinação segmentação + paginação

**MULTICS** (MIT + Bell Labs + General Electric, 1965) foi o primeiro SO a combinar segmentação com paginação:

```
Endereço MULTICS = [Nº segmento — 18 bits] + [Nº página — 6 bits] + [Deslocamento — 10 bits]
```

Mecanismo:
1. Segmento de descritores indexado pelo número de segmento → descritor aponta para tabela de páginas
2. Tabela de páginas indexada pelo número de página → quadro de página
3. Memória associativa de 16 entradas (hardware) para acelerar as combinações mais recentes

> [!info] Legado do MULTICS
> O MULTICS operou de 1965 até 30/10/2000 — 35 anos. A memória virtual de todas as CPUs Intel desde o 386 foi modelada seguindo esse modelo. Ver [[Proteção de Memória x86]].

---

## Comparação: paginação × segmentação

| Consideração | Paginação | Segmentação |
|---|---|---|
| Programador precisa estar ciente? | Não | Sim |
| Espaços de endereço lineares | 1 | Muitos |
| Espaço virtual > memória física? | Sim | Sim |
| Tabelas de tamanho variável? | Não (trabalhoso) | Sim (direto) |
| Proteção por tipo de objeto? | Difícil | Natural |
| Motivação original | Simular memórias grandes | Múltiplos espaços de endereço |

---

## Core i7: segmentação com LDT/GDT

O Core i7 suporta paginação pura, segmentação pura e segmentação com paginação. A segmentação usa:
- **LDT** (Local Descriptor Table): segmentos locais de cada programa (código, dados, pilha)
- **GDT** (Global Descriptor Table): segmentos do sistema (SO); uma única GDT por sistema

Seletores de 16 bits identificam o segmento. O seletor carrega automaticamente o descritor na MMU. Quando a paginação está habilitada, o endereço linear resultante passa pelo hardware de paginação em dois níveis. Ver [[Proteção de Memória x86]].

---

## Ver também
- [[Memória Virtual]] — paginação: alternativa à segmentação; comparação conceitual
- [[Proteção de Memória x86]] — LDT, GDT, descritores de segmento no x86
- [[Paginação]] — alternativa com blocos de tamanho fixo; comparação na nota
- [[Paginação x86]] — paginação sobre endereços lineares produzidos pela segmentação
- [[Organização de Memória x86]] — endereçamento segmentado no x86
- [[Sistema Operacional]] — segmentação como mecanismo do nível OSM
