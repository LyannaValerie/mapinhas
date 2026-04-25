---
title: Relocação de Código
aliases:
  - relocation
  - relocação
  - code relocation
tags:
  - computação/sistemas
source: "Linking and Loading"
created_by: Codex
---

# Relocação de Código

criado pelo Codex

## Definição

**Relocação** é o ajuste de endereços embutidos em código ou dados quando a posição final de segmentos e símbolos fica conhecida.

## Relações (SPO)

- [[Arquivo Objeto]] → registra → locais que precisam de relocação
- [[Ligador]] → executa → relocação em link time
- [[Carregador]] → executa → relocação em load time
- [[Ligação Dinâmica]] → executa → relocação para símbolos compartilhados
- [[PIC e GOT]] → reduz → necessidade de modificar segmento de código

## Tipos práticos

- **link-time relocation**: resolvida ao gerar executável final.
- **load-time relocation**: resolvida quando o programa é carregado.
- **runtime relocation**: resolvida por ligação dinâmica ou carregamento explícito.

## Risco

Relocar código compartilhado modifica páginas de instrução. Isso pode quebrar compartilhamento entre processos e aumentar memória privada. Por isso bibliotecas compartilhadas usam [[PIC e GOT]].

