---
title: Segmentação em Sistemas Operacionais
aliases:
  - segmentação de SO
  - segmentation
tags:
  - computação/sistemas
  - sistemas-operacionais/memória
source: "Sistemas Operacionais Modernos, cap. 3"
created_by: Codex
---

# Segmentação em Sistemas Operacionais

Nota criada pelo Codex.

## Definição

Modelo de organização de memória que divide o [[Espaço de Endereçamento]] em segmentos lógicos, como código, dados, pilha, heap ou tabelas.

## Papel técnico

Diferente da paginação, segmentos podem ter tamanhos diferentes e refletem unidades semânticas do programa. Isso facilita proteção e compartilhamento por região lógica, mas também pode introduzir fragmentação externa.

## Funcionamento

**Endereço lógico:** par `(número de segmento, deslocamento)`. O hardware consulta a **tabela de segmentos** — um par `(base, limite)` por segmento.

**Tradução:**

1. Hardware extrai número de segmento e deslocamento.
2. Verifica se `deslocamento < limite` do segmento → se não → trap de violação.
3. Verifica bits de proteção do segmento (leitura, escrita, execução).
4. Endereço físico = `base + deslocamento`.

**Segmentos típicos:** código (RX), dados (RW), pilha (RW), heap (RW), segmentos compartilhados (bibliotecas).

**Fragmentação externa:** segmentos têm tamanhos variáveis; buracos entre segmentos na RAM físicos não podem ser usados por segmentos maiores. Compactação periódica pode resolver, mas é cara.

**Segmentação com paginação (x86):** abordagem híbrida. x86 usa tabelas de segmentos (GDT/LDT) para produzir endereço linear, que é então paginado pela [[MMU]]. No modo 64-bits, segmentação é praticamente desabilitada (bases zeradas) e só a paginação opera.

**Segmentação pura vs. paginação:** segmentação reflete estrutura lógica do programa; paginação facilita gerenciamento físico sem fragmentação externa. MULTICS combinava as duas por razões históricas.

## Relações (SPO)

- Segmentação em Sistemas Operacionais -> divide -> espaço de endereçamento por unidades lógicas
- Segmentação em Sistemas Operacionais -> facilita -> proteção por região
- Segmentação em Sistemas Operacionais -> pode causar -> fragmentação externa
- Segmentação em Sistemas Operacionais -> pode combinar com -> [[Paginação em Sistemas Operacionais]]
- [[Gerenciamento de Memória]] -> pode usar -> Segmentação em Sistemas Operacionais

