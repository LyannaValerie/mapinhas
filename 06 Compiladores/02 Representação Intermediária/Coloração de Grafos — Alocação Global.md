---
title: Coloração de Grafos — Alocação Global
tags:
  - compiladores
  - back-end
  - registradores
  - grafos
  - ec/cap13
aliases:
  - Graph Coloring Register Allocation
  - Alocação Global por Coloração
  - Chaitin
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Compiladores]]

# Coloração de Grafos — Alocação Global

Paradigma dominante para alocação global de registradores.  
Modela o problema como **coloração de grafo**: cores = registradores físicos, nós = live ranges, arestas = interferências.

## Live Ranges Globais

Live range global $lr_i$: fecho transitivo de pares definição-uso.  
Para uso $u$ em $lr_i$: $lr_i$ inclui toda definição $d$ que alcança $u$.  
Para definição $d$ em $lr_i$: $lr_i$ inclui todo uso $u$ que $d$ alcança.

Diferença vs local:
- Local: live range = intervalo em linha reta; custo de spill uniforme
- Global: live range = rede em múltiplos blocos; custo de spill varia por frequência de execução

## Grafo de Interferência $I$

Dois live ranges $lr_i$ e $lr_j$ **interferem** se ambos estão vivos no mesmo ponto do programa → não podem compartilhar registrador.

$I$: grafo não-direcionado onde nó = live range, aresta = interferência.

$I$ é $k$-colorável $\Leftrightarrow$ $k$ registradores são suficientes sem spill.

## Custo de Spill

Estimativa do custo de manter valor em memória ao invés de registrador:

$$\text{custo}(lr_i) = \sum_{\text{def/uso em } lr_i} \text{freq}(\text{bloco})$$

Operações em loops têm frequência estimada por fator de $10^{\text{profundidade}}$.  
Alocador minimiza soma de custos de spill.

## Top-Down Coloring (Chow)

1. Ordena live ranges por prioridade: $\text{custo}/\text{grau}$
2. Para cada live range em ordem decrescente de prioridade:
   - Se grau $< k$: tentar atribuir cor não usada pelos vizinhos
   - Senão: marcar como spill candidate
3. Inserir loads/stores para spilled values; re-executar alocação

Interferência imprecisa (Chow): $lr_i$ e $lr_j$ interferem se ambos vivos no **mesmo bloco** (não mesmo ponto). Mais rápido de construir; superestima grau; impede coalescing por grafo.

## Bottom-Up Coloring (Chaitin)

Fase de **simplificação**:
```
enquanto grafo não vazio:
    se existe nó com grau < k:
        empilha nó; remove do grafo
    senão (todos constrained):
        seleciona spill candidate (menor custo)
        empilha; remove do grafo; marca como possível spill
```

Fase de **seleção**:
```
enquanto pilha não vazia:
    desempilha nó
    atribui cor ∉ cores dos vizinhos já coloridos
    se não existe cor disponível: spill real
```

Se spill real ocorre: inserir loads/stores, recalcular live ranges, repetir.

## Coalescing de Cópias

Se $lr_i$ e $lr_j$ estão conectados apenas por operação de cópia e **não interferem** entre si: podem ser fundidos em $lr_{ij}$.  
Resultado: elimina a operação de cópia do código final.

**Conservative coalescing**: funde $lr_i$ e $lr_j$ somente se $lr_{ij}$ tem menos de $k$ vizinhos de grau significativo (grau $\geq k$).  
Garante que a fusão não torna o grafo mais difícil de colorir.

Após coalescing conservativo ainda existem cópias não elimináveis: aceitar ou usar coalescing agressivo com possível re-coloração.

## Linear Scan Allocation

Alternativa mais rápida ao graph coloring; usado em JIT compilers.

1. Calcular live intervals (start, end) para cada valor
2. Ordenar por início
3. Para cada interval em ordem:
   - Se há registrador livre: atribuir
   - Senão: spill o interval com maior end (ou o atual se este tem menor end)

Complexidade $O(n \log n)$. Não é ótimo mas é rápido.

## Alocação Global via SSA (13.5.2)

Nomes SSA como live ranges: cada nome SSA = um live range.  
**Propriedade chordal**: grafo de interferência de SSA é cordal (toda ciclo de comprimento ≥ 4 tem corda).  
Grafos cordais → coloração ótima em tempo polinomial.

Complicação: $\phi$-funções inserem cópias; não podem ser coalesced arbitrariamente (preservar propriedade chordal).  
Requer algoritmo de coalescing específico não baseado em grafo de interferência.

## Variações Avançadas (13.5.1)

| Variação | Ideia |
|----------|-------|
| Imprecise interference graph | interferência por bloco (Chow); mais rápido, menos preciso |
| Quebrar grafo em componentes | colorir subgrafos independentes; economiza O(N²) |
| Rematerialização | recalcular valor ao invés de fazer load; reduz custo de spill |
| Scalar replacement | introduz variável escalar para reusar elemento de array; expõe mais live ranges a allocator |
| Register promotion | análise de ponteiro → promove valor baseado em ponteiro para registrador em loop |

> [!warning] Complexidade
> Encontrar coloração ótima de grafo de interferência geral é NP-completo.  
> Top-down e bottom-up são heurísticas; resultados podem divergir dependendo do programa.

## Relações

- [[Alocação de Registradores]] — visão geral + alocação local
- [[Forma SSA]] — base para alocação SSA-based; $\phi$-funções e live ranges
- [[Análise de Fluxo de Dados]] — liveness analysis para construir live ranges
- [[Escalonamento de Instruções]] — interação com lifetimes e pressão
- [[ILOC — Linguagem Intermediária]] — exemplos de código de entrada
