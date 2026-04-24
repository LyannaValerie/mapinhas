---
title: Escalonamento Regional e Software Pipelining
tags:
  - compiladores
  - back-end
  - escalonamento
  - ec/cap12
aliases:
  - Regional Scheduling
  - Software Pipelining
  - Trace Scheduling
  - EBB Scheduling
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Compiladores]]

# Escalonamento Regional e Software Pipelining

Extensões do [[List Scheduling — Escalonamento Local]] para regiões maiores que um bloco básico.  
Objetivo: dar ao escalonador mais contexto e mais operações para explorar ILP.

---

## 12.4 Escalonamento Regional

### 12.4.1 Escalonamento de EBBs (Extended Basic Blocks)

**EBB**: conjunto $\{B_1, B_2, \ldots, B_n\}$ onde $B_1$ tem múltiplos predecessores e todos os outros $B_i$ têm exatamente um predecessor dentro da EBB.

Estratégia: tratar caminhos dentro da EBB como blocos únicos para o escalonador.  
Ex.: caminho $\langle B_1, B_2, B_4 \rangle$ tratado como bloco único.

**Complicações**:
- **Prefixos compartilhados**: $B_1$ aparece em múltiplos caminhos; ops movidas em $B_1$ afetam todos os caminhos
- **Saídas prematuras**: op movida para fora de $B_1$ pode estar ausente no caminho que sai antes

**Código de compensação** (*compensation code*): ops inseridas em bloco $B_i$ para corrigir efeitos de movimento de código ao longo de caminho que não inclui $B_i$.

| Direção do movimento | Compensação necessária |
|---------------------|----------------------|
| Forward (op movida para depois) | cópia da op inserida no bloco off-path |
| Backward (op movida para antes) | op inserida no bloco off-path; pode ter efeito colateral → renomear ou compensar |

**Ordem de escalonamento**: caminhos mais frequentes primeiro (menos código de compensação).  
Informação de frequência: profiling ou estimativa estática.

Otimização: usar informação de *liveness* — se resultado não é vivo na entrada do bloco off-path, nenhuma compensação necessária para movimento forward.

---

### 12.4.2 Trace Scheduling

Seleciona um **trace** = caminho de execução frequente através do CFG (sequência de blocos).  
Escalona o trace inteiro como um único bloco.  
Insere código de compensação nas entradas laterais e saídas do trace.  
Repete até que todas as ops estejam escalonadas.

Diferença em relação ao EBB: trace pode cruzar blocos com múltiplos predecessores (EBB não pode).  
Maior contexto → mais oportunidades de escalonamento, mais compensação.

---

### 12.4.3 Cloning para Contexto

Problema: bloco com múltiplos predecessores força escalonador a usar suposições conservadoras.  
Solução: **clonar** o bloco → cada clone recebe um único predecessor.

```
Antes:           Depois:
Entry            Entry
  ↓                ↓
 B1              B1'  (clone para caminho de Entry)
  ↓↑               ↓↑
 B2              B2
  ↓                ↓
 B1              B1   (original para caminho de B2)
```

Caso de uso típico: programas **tail-recursive** — após otimização de tail call, o bloco de entrada tem dois predecessores (entrada do procedimento + back-edge). Clonar $B_1$ dá contexto único a cada cópia.

Custo: duplicação de código. Benefício: melhor schedule para o caminho mais frequente.

---

## 12.5 Software Pipelining

Técnica de escalonamento de **loops** que sobrepõe iterações sucessivas.  
Em cada ciclo do kernel, operações de 2 ou 3 iterações diferentes estão em execução simultânea.

### Estrutura

```
[Prólogo] → [Kernel (repetido)] → [Epílogo]
```

- **Prólogo**: preenche o pipeline (executa início das primeiras iterações)
- **Kernel**: corpo fixo do loop pipelined; cada iteração do kernel processa ~1/k de cada iteração ativa do loop original
- **Epílogo**: drena o pipeline (completa as últimas iterações)

Código total ≈ dobro do loop original (prólogo + epílogo).

### Intervalo de Iniciação (II)

Ciclos entre início de iterações consecutivas no kernel.

$$\text{II} = \max(\text{RC},\, \text{DC})$$

**Resource Constraint (RC)**:
$$\text{RC} = \max_u \left\lceil \frac{I_u}{N_u} \right\rceil$$
$I_u$ = operações do loop que usam unidade $u$; $N_u$ = cópias da unidade $u$.

**Dependence Constraint (DC)**:
$$\text{DC} = \max_r \left\lceil \frac{d_r}{k_r} \right\rceil$$
$d_r$ = comprimento do ciclo de recorrência $r$ em ciclos; $k_r$ = ops no ciclo $r$.

### Algoritmo (12.5.2)

1. Calcular II a partir de RC e DC
2. Tentar escalonar todas as ops do loop dentro de II ciclos
3. Se não couber: aumentar II e repetir
4. Gerar prólogo e epílogo a partir do kernel

> [!tip] Quando usar
> Software pipelining é mais efetivo em loops pequenos e computacionalmente intensivos onde o número de ops é pequeno em relação ao comprimento do caminho crítico — o loop não consegue manter o hardware ocupado sem sobreposição de iterações.

> [!warning] Requisito
> Otimizações de análise de loop (substituição de teste de saída, OSR) devem ser aplicadas antes do software pipelining para máxima efetividade.

## Relações

- [[List Scheduling — Escalonamento Local]] — motor subjacente de todos os métodos regionais
- [[Escalonamento de Instruções]] — problema geral
- [[Paralelismo em Nível de Loop]] — sobreposição de iterações em hardware
- [[OSR — Redução de Força por Operador]] — habilitador para software pipelining
- [[Transformações Habilitadoras — Cloning e Unswitching]] — cloning de bloco para contexto
