---
title: Troca de Processos
aliases:
  - swapping
  - swap de processos
tags:
  - computação/sistemas
  - sistemas-operacionais/memória
source: "Sistemas Operacionais Modernos, cap. 3"
created_by: Codex
---

# Troca de Processos

Nota criada pelo Codex.

## Definição

Técnica em que um [[Processo]] inteiro ou partes relevantes de seu estado de memória são movidos entre [[Memória]] principal e armazenamento secundário.

## Papel técnico

Swapping permite executar mais processos do que caberiam simultaneamente na RAM. O custo é alto: mover imagens de processo entre disco e memória consome tempo de E/S e pode fragmentar a memória disponível.

## Funcionamento

Quando mais processos precisam executar do que a RAM comporta, o SO escolhe um processo para remover temporariamente:

1. Salva a imagem completa do processo (código, dados, pilha) no disco.
2. Libera os quadros de página que esse processo ocupava.
3. Carrega um processo esperando do disco para os quadros liberados.
4. Quando o processo removido for agendado novamente, repete o ciclo em sentido contrário.

**Problema de realocação:** ao retornar do disco, o processo pode ser carregado em endereço físico diferente. Registradores base e limite resolvem isso: SO atualiza `BASE` ao recarregar.

**Fragmentação externa:** após múltiplos swaps, surgem "buracos" entre processos na RAM. A técnica de *compactação* move todos os processos para baixo e reúne os buracos — mas é cara. Em uma máquina de 16 GB copiando 8 bytes/8 ns, a compactação levaria ~16 segundos.

**Alocação dinâmica:** processos que crescem (heap, pilha) podem precisar de espaço extra. Estratégia comum: alocar uma área de folga ao redor do processo; se insuficiente, mover ou fazer swap.

## Relações (SPO)

- Troca de Processos -> libera -> [[Memória]]
- Troca de Processos -> aumenta -> grau de multiprogramação
- Troca de Processos -> depende -> armazenamento secundário
- [[Memória Virtual em Sistemas Operacionais]] -> generaliza -> Troca de Processos
- troca excessiva -> causa -> [[Thrashing]]

