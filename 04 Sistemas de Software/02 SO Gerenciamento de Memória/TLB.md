---
title: TLB
aliases:
  - Translation Lookaside Buffer
  - buffer de tradução
tags:
  - computação/sistemas
  - sistemas-operacionais/memória
source: "Sistemas Operacionais Modernos, cap. 3"
created_by: Codex
---

# TLB

Nota criada pelo Codex.

## Definição

Cache de traduções mantido pela [[MMU]] para acelerar a conversão entre página virtual e quadro físico.

## Papel técnico

Sem TLB, cada acesso à memória paginada poderia exigir consulta à [[Tabela de Páginas]], aumentando muito o custo de execução. Com TLB, traduções recentes ficam próximas do hardware de tradução.

## Funcionamento

A TLB é um hardware associativo pequeno (tipicamente 8 a 256 entradas) dentro da [[MMU]]. Cada entrada armazena:

```
[ válida | página virtual | quadro físico | proteção | modificada ]
```

**Busca paralela (associativa):** ao receber um endereço virtual, a MMU compara o número de página virtual com *todas* as entradas da TLB simultaneamente. Hardware especial realiza isso em um único ciclo de clock.

**TLB hit:** quadro obtido diretamente → endereço físico formado sem consultar a [[Tabela de Páginas]].

**TLB miss:** MMU busca na tabela de páginas em RAM, carrega a entrada na TLB (substituindo uma entrada antiga se a TLB estiver cheia), retorna o quadro.

**Troca de contexto:** ao trocar de processo, as entradas da TLB do processo anterior tornam-se inválidas. Opções:
- Flush total: invalida todas as entradas (simples, mas caro).
- ASID (*Address Space ID*): cada entrada carrega um ASID; entradas de outros processos são ignoradas sem flush.

**Taxa de acerto:** programas com boa localidade de referência atingem >99% de hits; a penalidade de um miss (consulta à tabela em RAM) só ocorre raramente.

## Relações (SPO)

- TLB -> acelera -> tradução de endereços
- TLB -> cacheia -> [[Entrada de Tabela de Páginas]]
- falta na TLB -> exige -> consulta à [[Tabela de Páginas]]
- troca de contexto -> pode invalidar -> entradas da TLB
- [[Paginação em Sistemas Operacionais]] -> depende de desempenho de -> TLB

