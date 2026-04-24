---
title: List Scheduling — Escalonamento Local
tags:
  - compiladores
  - back-end
  - escalonamento
  - ec/cap12
aliases:
  - List Scheduling
  - Escalonamento em Lista
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Compiladores]]

# List Scheduling — Escalonamento Local

Heurística gulosa dominante para escalonamento de instruções desde o fim dos anos 1970.  
Opera sobre um **único bloco básico** (código em linha reta — sem ramificações internas).

## Quatro Passos

### 1. Renomear para Eliminar Antidependências

Cada definição recebe nome único. Elimina restrições WAR e WAW do grafo.  
Não é obrigatório, mas amplia o espaço de schedules válidos.

### 2. Construir Grafo de Dependência $D$

Percorre o bloco de baixo para cima.  
Cada operação = nó. Aresta $i \to j$: $j$ usa resultado de $i$, anotada com latência de $i$.

### 3. Atribuir Prioridades

Esquemas comuns (podem ser combinados para desempate):

| Critério | Efeito |
|----------|--------|
| Comprimento do caminho ponderado por latência | clássico; prioriza critical path |
| Número de sucessores imediatos em $D$ | favorece breadth-first; mantém Ready maior |
| Total de descendentes em $D$ | amplifica efeito anterior |
| Ciclo mais cedo possível | baseado em restrições de latência |
| Ciclo mais tarde possível | evita atrasar ops críticas |

### 4. Escalonar (Algoritmo de Lista)

```
Cycle ← 1
Ready ← folhas de D        // ops sem predecessores
Active ← ∅
while (Ready ∪ Active ≠ ∅)
    para cada op ∈ Active
        se S(op) + delay(op) ≤ Cycle então
            remove op de Active
            para cada sucessor s de op em D
                se s está pronto → adiciona s a Ready
    se Ready ≠ ∅ então
        remove op de Ready (maior prioridade)
        S(op) ← Cycle
        adiciona op a Active
    Cycle ← Cycle + 1
```

`S(op)` = ciclo de emissão de op.  
Op está *pronta* quando todos seus predecessores completaram (S + delay ≤ Cycle atual).

## Latências Variáveis

Loads: latência depende do nível de cache atingido em runtime.  
Estratégia: **maximizar distância** entre load e seus usos no schedule.  
Heurística: inicializa delay = 1; itera sobre ops independentes no grafo para propagar delays maiores.  
Não prejudica execução sem miss; maximiza tolerância a miss de cache.

## Escalonamento Forward vs Backward

| Direção | Ordena por | Vantagem |
|---------|-----------|----------|
| Forward (padrão) | ciclo mais cedo | intuitivo; minimiza stalls |
| Backward | ciclo mais tarde | pode reduzir lifetimes de registradores |

Backward scheduling útil quando pressão de registradores é crítica.

## Extensão para Múltiplas Unidades Funcionais

Cada ciclo pode emitir uma op por unidade funcional disponível.  
Restrições adicionais:
- ops restritas a uma única FU vs ops que podem usar qualquer FU
- registradores particionados (ex: inteiro vs ponto flutuante): op deve ir para FU com acesso à partição de seus operandos
- estado das FUs deve ser rastreado no `Active`

## Escalonamento em Bloco de Término

Branch de fim de bloco tem restrição de posição.  
Branch de 1 ciclo: deve ocupar o último ciclo.  
Branch de 2 ciclos: deve ocupar o penúltimo ciclo.  
Delay slots: slot após o branch executa enquanto branch processa; pode ser preenchido com op útil.

## Melhorias de Eficiência

- Fila de prioridade para `Ready` (heap): $O(\log n)$ por operação
- Análise incremental de dependências: evita recalcular $D$ inteiro a cada iteração
- Precomputação de prioridades em uma passagem sobre $D$

> [!warning] Interação com Alocação de Registradores
> Mover operações pode estender lifetimes de valores além do ponto onde seriam usados.  
> Lifetimes mais longos = mais pressão = possíveis spills adicionais.  
> Escalonador deve monitorar pressão ao mover operações.

## Relações

- [[Escalonamento de Instruções]] — contexto e problema
- [[Escalonamento Regional e Software Pipelining]] — extensões multi-bloco
- [[Alocação de Registradores]] — interação pressão de registradores
- [[ILOC — Linguagem Intermediária]] — formato de ops no grafo $D$
