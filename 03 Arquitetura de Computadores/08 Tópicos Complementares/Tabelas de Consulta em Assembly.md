---
title: Tabelas de Consulta em Assembly
aliases:
  - lookup table
  - table lookup
  - tabela de lookup
  - tabela de consulta
  - XLAT x86
tags:
  - computação/arquitetura
  - computação/assembly
  - computação/otimização
date: 2026-04-24
source: "Advanced Arithmetic, cap. 8"
created_by: "criado pelo Codex"
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Tabelas de Consulta em Assembly

Tabela de consulta é um array inicializado, normalmente imutável durante a execução, usado para substituir cálculo por acesso à memória. Em assembly, é técnica direta para acelerar funções pequenas, classificação de bytes, conversões e controle de fluxo.

## Relações (SPO)

- Tabela de consulta → troca → tempo de CPU por espaço de memória
- Entrada da função → indexa → posição na tabela
- Tabela estática → pode ficar em → seção read-only
- Domínio finito → viabiliza → pré-computação completa
- Domínio grande → aumenta → custo espacial
- `xlat` → consulta → tabela de bytes em x86

---

## Modelo

Em vez de calcular:

```text
y = f(x)
```

pré-computa-se:

```text
tabela[x] = f(x)
```

Depois, a execução vira indexação:

```text
y = tabela[x]
```

Esse padrão é útil quando `f(x)` é cara e `x` pertence a domínio pequeno ou normalizável.

---

## Vantagens

- reduz número de instruções executadas
- remove branches de cadeias condicionais pequenas
- estabiliza latência para domínios discretos
- simplifica conversões repetitivas

---

## Limites

Tabelas só funcionam bem quando o domínio é limitado. Funções com domínio grande ou contínuo exigem:

- quantização do índice
- tabela parcial
- interpolação
- fallback para cálculo normal

O ganho também depende de cache. Tabela grande pode perder localidade e ficar mais lenta que cálculo direto.

> [!warning]
> Lookup table não é otimização automática. Se a tabela não cabe bem em cache ou se o cálculo original é barato, o acesso à memória pode dominar o custo.

---

## Uso em x86

`xlat` é instrução x86 clássica para consultar tabela de bytes. Fora desse caso estreito, a forma geral é calcular endereço base + índice escalado e carregar da memória.

```asm
; forma conceitual
movzx eax, byte [table + ebx]
```

Para entradas maiores que 1 byte, o índice precisa ser escalado pelo tamanho do elemento.

---

## Ver também

- [[Otimização de Código]] — troca entre custo de execução e espaço
- [[Cache]] — localidade e custo de acesso à memória
- [[Instruções de String x86]] — operações especializadas em baixo nível
- [[Aritmética de Multiprecisão]] — outro caso em que assembly expõe controle fino sobre dados
- [[Linguagem Assembly]] — modelo operacional das instruções

