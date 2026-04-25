---
title: Paginação em Sistemas Operacionais
aliases:
  - paginação de SO
  - paging
tags:
  - computação/sistemas
  - sistemas-operacionais/memória
source: "Sistemas Operacionais Modernos, cap. 3"
created_by: Codex
---

# Paginação em Sistemas Operacionais

Nota criada pelo Codex.

## Definição

Técnica de [[Memória Virtual em Sistemas Operacionais]] que divide o espaço virtual em páginas e a memória física em quadros de mesmo tamanho.

## Papel técnico

Paginação remove a exigência de que um processo ocupe região física contígua. A [[Tabela de Páginas]] registra onde cada página virtual está, se está presente na RAM e quais permissões de acesso se aplicam.

## Funcionamento

**Estrutura do endereço virtual:**

```
[ número de página virtual | deslocamento dentro da página ]
```

Para páginas de 4 KB (2¹²), os 12 bits menos significativos são o deslocamento; os bits restantes identificam a página.

**Tradução pela MMU:**

1. Extrai número de página virtual do endereço.
2. Usa esse número como índice na [[Tabela de Páginas]] (consultando a [[TLB]] primeiro).
3. Obtém número do quadro físico.
4. Endereço físico = `[número de quadro | deslocamento]`.

**Tamanho de páginas:** tipicamente 4 KB. x86-64 suporta 4 KB, 2 MB e 1 GB. Páginas maiores reduzem overhead de tabela e pressão na TLB, mas aumentam fragmentação interna.

**Página ausente:** bit de presente = 0 na entrada da tabela → MMU gera [[Falha de Página]]. SO traz a página do disco, preenche o quadro, seta bit de presente = 1, e reinicia a instrução.

**Vantagem:** processo não precisa ocupar região física contígua. Cada página pode estar em qualquer quadro.

## Relações (SPO)

- Paginação em Sistemas Operacionais -> divide -> [[Espaço de Endereçamento]]
- Paginação em Sistemas Operacionais -> mapeia -> páginas para quadros
- Paginação em Sistemas Operacionais -> usa -> [[Tabela de Páginas]]
- acesso a página ausente -> gera -> [[Falha de Página]]
- [[TLB]] -> acelera -> Paginação em Sistemas Operacionais

