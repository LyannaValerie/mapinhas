---
title: Memória Virtual em Sistemas Operacionais
aliases:
  - memória virtual de SO
  - virtual memory
tags:
  - computação/sistemas
  - sistemas-operacionais/memória
source: "Sistemas Operacionais Modernos, cap. 3"
created_by: Codex
---

# Memória Virtual em Sistemas Operacionais

Nota criada pelo Codex.

## Definição

Abstração que permite a um processo usar um [[Espaço de Endereçamento]] maior ou mais regular do que a [[Memória]] física disponível naquele instante.

## Papel técnico

Memória virtual mantém partes ativas do processo na RAM e deixa partes inativas no armazenamento secundário. Quando o processo acessa uma parte ausente, ocorre [[Falha de Página]] e o sistema traz o conteúdo necessário.

## Funcionamento

Antes da memória virtual, programas maiores que a RAM eram divididos manualmente em *overlays* — fragmentos carregados sob demanda pelo programador. Isso era trabalhoso e propenso a erro.

A ideia central (Fotheringham, 1961): o espaço de endereçamento virtual do processo é dividido em **páginas** de tamanho fixo. Nem todas as páginas precisam estar na RAM ao mesmo tempo. O hardware (MMU) mapeia cada página para um **quadro** físico ou detecta quando a página está ausente.

**Ciclo de execução com memória virtual:**

1. CPU gera endereço virtual e envia à MMU.
2. MMU verifica se a página está presente (bit de presente na [[Tabela de Páginas]]).
3. Se sim → acesso físico imediato.
4. Se não → **[[Falha de Página]]**: SO é acionado, busca a página no disco, carrega em um quadro livre (ou substitui uma página vítima), atualiza a tabela, reinicia a instrução.

**Vantagem em multiprogramação:** enquanto um processo espera o disco (lenta E/S), a CPU é cedida a outro processo, aumentando utilização.

## Relações (SPO)

- Memória Virtual em Sistemas Operacionais -> implementa -> [[Espaço de Endereçamento]]
- Memória Virtual em Sistemas Operacionais -> usa -> [[Paginação em Sistemas Operacionais]]
- Memória Virtual em Sistemas Operacionais -> depende -> [[Tabela de Páginas]]
- Memória Virtual em Sistemas Operacionais -> pode causar -> [[Thrashing]]
- [[Gerenciamento de Memória]] -> controla -> Memória Virtual em Sistemas Operacionais

