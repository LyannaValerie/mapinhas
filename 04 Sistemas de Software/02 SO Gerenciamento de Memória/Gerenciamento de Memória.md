---
title: Gerenciamento de Memória
aliases:
  - memory management
tags:
  - computação/sistemas
  - sistemas-operacionais/memória
source: "Sistemas Operacionais Modernos, cap. 3"
created_by: Codex
---

# Gerenciamento de Memória

Nota criada pelo Codex.

## Definição

Subsistema do [[Sistema Operacional]] responsável por controlar quais regiões de [[Memória]] estão em uso, alocar memória para [[Processo|processos]], liberar regiões ociosas e impor isolamento entre espaços de execução.

## Papel técnico

O gerenciador de memória transforma uma hierarquia física limitada em abstrações úteis para programas. Ele combina [[Espaço de Endereçamento]], [[Troca de Processos]], [[Memória Virtual em Sistemas Operacionais]] e proteção para permitir multiprogramação sem acesso arbitrário entre processos.

## Funcionamento

Historicamente, o gerenciamento evoluiu em camadas:

1. **Sem abstração** (antes de 1980): programas acessam RAM física diretamente. Impossível ter dois processos simultâneos sem conflito — gravar no endereço 2000 de um programa apaga o que o outro programa tinha ali.

2. **Registradores base e limite**: solução simples. Ao despachar um processo, SO carrega o endereço inicial do processo em `base` e o tamanho em `limite`. A MMU soma base a cada endereço gerado. Se o resultado excede `base + limite`, gera trap. Cada processo "enxerga" o mesmo intervalo 0..limite, mas mapeado a regiões físicas diferentes.

3. **Swapping**: quando todos os processos não cabem na RAM ao mesmo tempo, o SO move processos inteiros para o disco e traz de volta quando necessário.

4. **Gerenciando a memória livre**: SO mantém registro das regiões livres usando:
   - *Bitmap*: um bit por bloco; fácil de implementar, mas busca de região livre é lenta.
   - *Lista encadeada*: cada nó representa segmento livre ou ocupado com (tipo, início, tamanho). Alocações usam estratégias como *first fit*, *next fit*, *best fit* ou *worst fit*.

5. **Memória virtual com paginação**: abordagem moderna. Desacopla espaço virtual do físico em unidades pequenas (páginas); o SO mantém apenas as páginas ativas na RAM.

## Relações (SPO)

- Gerenciamento de Memória -> aloca -> memória para [[Processo|processos]]
- Gerenciamento de Memória -> implementa -> [[Espaço de Endereçamento]]
- Gerenciamento de Memória -> usa -> [[Paginação em Sistemas Operacionais]]
- Gerenciamento de Memória -> evita -> [[Thrashing]]
- [[Sistema Operacional como Gerenciador de Recursos]] -> inclui -> Gerenciamento de Memória

