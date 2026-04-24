---
title: Back End do Compilador
tags:
  - compiladores
  - programação
aliases:
  - compiler back end
  - code generation
  - geração de código
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Compiladores]]

# Back End do Compilador

Fase final: mapeia [[Representação Intermediária (IR)|IR]] para código da máquina alvo usando os recursos limitados dessa máquina.

## Três passes principais

### 1. Instruction Selection (Seleção de Instruções)

Reescreve operações IR em operações da máquina alvo.

- Usa programação dinâmica para selecionar operações eficientes
- Pode aproveitar instruções especiais da arquitetura
- Durante este pass, usa **registradores virtuais** ilimitados — deixa para o allocator decidir

### 2. Register Allocation (Alocação de Registradores)

Mapeia registradores virtuais (ilimitados) para registradores físicos reais.

- Máquina tem número limitado de registradores
- Valores que não cabem em registradores vão para a memória (*spill*)
- Usa algoritmos gulosos (greedy)
- Resultado pode criar restrições para o instruction scheduler

### 3. Instruction Scheduling (Escalonamento de Instruções)

Reordena operações para melhorar desempenho.

- Move operações de load para longe das operações aritméticas que usam o valor (esconde latência)
- Deve respeitar dependências de dados
- Pode ser constrangido pelas decisões de register allocation

## Interdependências

```
IR → [Inst. Selection] → [Reg. Allocation] → [Inst. Scheduling] → Código Nativo
```

> [!note] Interação entre passes
> Alocação de registradores pode criar dependências falsas que constrangem o escalonamento. Idealmente os três passes seriam resolvidos juntos — na prática, são sequenciais.

Ver [[Compilador — Estrutura de Fases]].
