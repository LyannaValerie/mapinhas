---
title: Loop Unrolling
aliases:
  - loop unrolling
  - loop unraveling
  - desenrolamento de loop
  - desenrolar loop
  - loop unroll
tags:
  - computação/arquitetura
date: 2026-04-24
source: "The Art of Assembly Language — Randall Hyde (HLA ed.), Cap. 7"
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Loop Unrolling

## Definição
Técnica de otimização que substitui um loop de N iterações por N cópias sequenciais do corpo do loop, eliminando as instruções de controle (decremento, comparação, salto condicional).

## Relações (SPO)
- Loop unrolling → reduz → overhead de controle de loop
- Loop unrolling → aumenta → tamanho do código (code size)
- Loop unrolling → aplica-se a → loops com número **fixo e conhecido** de iterações
- Processadores superescalares (Pentium+) → podem não se beneficiar → branch prediction hardware

---

## Motivação

Loop simples com overhead significativo:

```hla
// Loop original — 4 instruções por iteração, 1 útil
mov( 0, i );
LoopLbl:
    mov( i, ebx );
    mov( 0, A[ ebx*4 ] );   // única instrução útil
    dec( i );
    jns LoopLbl;
// 16 instruções para 4 operações úteis (razão 4:1)
```

Após unrolling (para N=4 iterações):

```hla
mov( 0, A[0] );    // sem controle de loop
mov( 0, A[4] );
mov( 0, A[8] );
mov( 0, A[12] );
// 4 instruções para 4 operações úteis (razão 1:1)
```

Melhoria potencial: 300–400% neste caso.

---

## Quando Aplicar

**Condições favoráveis:**
- Número de iterações fixo e conhecido em tempo de montagem
- Overhead de controle (dec + cmp + jmp) representa fração significativa do corpo do loop
- Corpo do loop pequeno (poucos casos de instruções)

**Condições desfavoráveis:**
- Número de iterações variável em tempo de execução
- Corpo do loop grande — o overhead de controle torna-se percentualmente irrelevante
- Processadores superescalares (Pentium e posteriores): branch prediction e otimizações de CPU para loops curtos podem tornar o unrolling contraproducente

> [!warning] Retornos Decrescentes
> À medida que o corpo cresce, a fração representada pelas instruções de controle diminui. Se o loop tem 36 instruções de corpo além de 4 de controle (10%), o ganho máximo é ~10% — muito menor que o 300–400% do exemplo acima.

---

## Variáveis de Indução

Loops podem ser reescritos eliminando a variável de controle original e mantendo-a apenas em registrador, reduzindo acessos à memória:

```hla
// Com variável em memória: shl + mov(i, ebx) por iteração
// Com variável de indução em registrador:
mov( 0, ebx );
LoopLbl:
    mov( 0, A[ ebx*4 ] );
    add( 4, ebx );
    cmp( ebx, 1024 );
    jb LoopLbl;
// Remove shl — EBX mantém diretamente o índice de byte
```

---

## Ver também
- [[Linguagem Assembly]]
- [[Otimização de Código]]
- [[Intel x86]]
- [[Estruturas de Controle Híbridas em HLA]]
