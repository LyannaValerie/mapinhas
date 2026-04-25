---
title: Algoritmo de Substituição de Páginas
aliases:
  - page replacement algorithm
  - substituição de páginas
tags:
  - computação/sistemas
  - sistemas-operacionais/memória
source: "Sistemas Operacionais Modernos, cap. 3"
created_by: Codex
---

# Algoritmo de Substituição de Páginas

Nota criada pelo Codex.

## Definição

Política usada pelo [[Sistema Operacional]] para escolher qual página retirar da RAM quando uma [[Falha de Página]] precisa carregar nova página e não há quadro livre.

## Critério técnico

Boa política reduz faltas futuras. O algoritmo ótimo remove a página que demorará mais a ser usada, mas exige conhecimento do futuro e serve como referência teórica. Implementações práticas aproximam esse ideal com bits de referência, bits de modificação e histórico de acessos.

## Exemplos

- NRU: classifica páginas por bits de referência e modificação.
- FIFO: remove a página mais antiga, mas pode descartar página importante.
- Segunda chance: melhora FIFO usando bit de referência.
- Relógio: implementa segunda chance com lista circular.
- LRU: remove página usada menos recentemente, mas é caro em forma exata.
- Envelhecimento e WSClock: aproximações eficientes baseadas em histórico recente.

## Mecanismos

**NRU (Not Recently Used):** classifica páginas em 4 classes com bits R (referenciada) e M (modificada): classe 0 (R=0,M=0), 1 (R=0,M=1), 2 (R=1,M=0), 3 (R=1,M=1). Remove página de menor classe. Bit R é zerado periodicamente (interrupção de relógio).

**FIFO:** fila de páginas carregadas; remove a mais antiga. Simples, mas pode remover páginas muito usadas (anomalia de Bélády).

**Segunda chance:** FIFO melhorado. Antes de remover, verifica bit R da página mais antiga. Se R=1, zera R, move a página para o fim da fila (segunda chance). Se R=0, remove.

**Relógio (clock):** implementa segunda chance com ponteiro circular sobre lista de páginas — evita mover entradas na fila. Ponteiro avança até encontrar página com R=0.

**LRU (Least Recently Used):** remove a página usada há mais tempo. Ótimo em teoria, mas a forma exata exige hardware especial (contador por página ou matriz de bits). Implementações práticas aproximam com envelhecimento (aging): registrador de k bits por página; a cada tick do relógio, shifta direita e ORa o bit R no bit mais significativo.

**WSClock:** combina relógio com modelo de [[Conjunto de Trabalho]]. Cada entrada armazena tempo do último uso. Ao girar o ponteiro: se R=1, zera R e atualiza tempo. Se R=0 e idade > τ (threshold) e não modificada → remove. Se modificada → agenda escrita no disco mas continua rodando o ponteiro.

## Relações (SPO)

- Algoritmo de Substituição de Páginas -> escolhe -> página vítima
- Algoritmo de Substituição de Páginas -> usa -> [[Entrada de Tabela de Páginas]]
- [[Conjunto de Trabalho]] -> orienta -> substituição de páginas
- substituição ruim -> aumenta -> [[Thrashing]]

