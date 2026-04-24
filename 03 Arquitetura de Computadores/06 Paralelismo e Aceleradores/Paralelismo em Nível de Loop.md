---
title: Paralelismo em Nível de Loop
aliases:
  - loop-level parallelism
  - paralelismo de loop
  - dependência de loop
  - loop-carried dependence
  - GCD test
tags:
  - computação/arquitetura
  - computação/ILP
date: 2026-04-22
source: "P&H Apêndice H §H.2"
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Paralelismo em Nível de Loop

Análise de loops para determinar se iterações podem executar em paralelo. Base para técnicas como [[Software Pipelining]] e [[VLIW — Very Long Instruction Word|VLIW]].

## Relações (SPO)

| Sujeito | Predicado | Objeto |
|---|---|---|
| loop paralelo | não possui | dependência loop-carried |
| dependência loop-carried | impede | paralelismo entre iterações |
| dependência within-iteration | não impede | paralelismo de loop |
| recorrência | é tipo de | dependência loop-carried |
| GCD test | detecta ausência de | dependência de memória entre iterações |
| renaming | elimina | name dependences |

## Tipos de Dependência

| Tipo | Sigla | Definição |
|---|---|---|
| True dependence | RAW | S2 lê valor escrito por S1 |
| Antidependence | WAR | S2 escreve onde S1 ainda vai ler |
| Output dependence | WAW | S1 e S2 escrevem na mesma variável |

**Name dependences** (WAR, WAW) não envolvem fluxo real de dados — podem ser eliminadas por renaming de registradores.

## Loop-Carried vs. Within-Iteration

```c
// Loop-carried: S1 em iteração i depende de S1 em iteração i-1
for (i=1; i<100; i++) {
    A[i+1] = A[i] + C[i];   // S1: usa A[i], produzido por S1 anterior
    B[i+1] = B[i] + A[i+1]; // S2: usa B[i], produzido por S2 anterior
}
// S1→S1: loop-carried (impede paralelismo de S1)
// S2→S1: within-iteration (S2 usa valor que S1 acabou de produzir na mesma iter)
// S2→S2: loop-carried

// Pode ser paralelizado com transformação: loop splitting em dois loops separados
```

## Loop sem Dependência Loop-Carried

```c
for (i=0; i<100; i++)
    x[i] = x[i] + s; // cada iteração independente → loop paralelo
```

## Dependência que Não Impede Paralelismo

```c
for (i=1; i<100; i++) {
    A[i]   = A[i] + B[i];    // S1: usa B[i], resultado de S2 da iteração ANTERIOR
    B[i+1] = C[i] + D[i];    // S2: produz B[i+1]
}
// S1 em iteração i depende de S2 em iteração i-1 (loop-carried)
// MAS não há ciclo de dependência — pode ser transformado:
//   A[1] = A[1] + B[1];
//   for (i=1; i<100; i++) { B[i+1] = C[i]+D[i]; A[i+1] = A[i+1]+B[i+1]; }
//   B[101] = C[100] + D[100];
// Agora iterações são independentes
```

## GCD Test

Teste necessário (não suficiente) para existência de dependência entre `A[a*i+c]` e `A[b*i+d]`:

**Dependência possível se e somente se** `GCD(a, b)` divide `(c - d)`.

Se GCD não divide `(c-d)` → os índices nunca coincidem → **sem dependência**.

```
A[2*i+3] vs A[4*i+1]: GCD(2,4)=2; c-d = 3-1 = 2; 2 divide 2 → possível dependência
A[2*i+3] vs A[4*i+2]: GCD(2,4)=2; c-d = 3-2 = 1; 2 não divide 1 → sem dependência
```

O GCD test é necessário mas não suficiente: pode indicar "dependência possível" quando não existe. Análises mais precisas (ex: Omega test) são mais caras.

## Recorrências

Dependência loop-carried onde o valor do acumulador de uma iteração depende da anterior:

```c
sum = sum + x[i]; // recorrência: sum_i = sum_{i-1} + x[i]
```

Com unrolling de N iterações + otimização algébrica, recorrências simples podem ser avaliadas uma vez por bloco:
```c
// 5× unrolled:
sum = sum + x1 + x2 + x3 + x4 + x5;
// → redução paralela: (x1+x2) + (x3+x4) + x5 em 3 passos em vez de 4
```

## Ver também

- [[Paralelismo em Nível de Instrução]] — contexto: ILP além de um loop
- [[Software Pipelining]] — explora paralelismo de loop com overlap de iterações
- [[VLIW — Very Long Instruction Word]] — usa análise de loop para preencher slots
- [[Escalonamento Dinâmico]] — alternativa: hardware detecta dependências em runtime
- [[Múltipla Emissão e Superescalar]] — beneficia-se de loops paralelos
