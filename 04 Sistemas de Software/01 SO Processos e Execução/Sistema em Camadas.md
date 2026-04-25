---
title: Sistema em Camadas
aliases:
  - layered system
  - arquitetura em camadas de sistema operacional
tags:
  - computação/sistemas
source: "Sistemas Operacionais Modernos, cap. 1"
created_by: Codex
date: 2026-04-25
---

%% criado pelo Codex %%

# Sistema em Camadas

## Definição

Estrutura de sistema operacional organizada em níveis hierárquicos, nos quais cada camada usa apenas serviços da camada imediatamente inferior e expõe serviços apenas para a camada imediatamente superior. Dependências circulares são proibidas.

## Sistema THE — primeiro sistema em camadas

O primeiro SO construído com essa abordagem foi o **sistema THE**, desenvolvido por E. W. Dijkstra (1968) e estudantes na Technische Hogeschool Eindhoven (Holanda) para o computador Electrologica X8.

| Camada | Função |
|---|---|
| 5 | O operador (processo de controle do sistema) |
| 4 | Programas de usuário |
| 3 | Gerenciamento de entrada/saída (abstrai dispositivos) |
| 2 | Comunicação operador–processo (console virtual por processo) |
| 1 | Memória e gerenciamento de tambor magnético |
| 0 | Alocação do processador e multiprogramação |

A camada 0 fornecia a multiprogramação básica. Acima dela, cada processo via a CPU como exclusivamente sua. A camada 1 implementava memória virtual via tambor magnético; acima dela, processos não precisavam se preocupar se estavam na RAM ou no tambor.

## MULTICS — anéis concêntricos

Uma generalização do conceito de camadas. Em vez de camadas lineares, MULTICS usou **anéis concêntricos**: anéis internos têm mais privilégio que externos. Para chamar código de um anel mais interno, o processo executa o equivalente de uma chamada de sistema (instrução TRAP), com os parâmetros verificados antes de a chamada prosseguir.

## Papel técnico

A divisão em camadas reduz acoplamento: cada camada pode ser depurada e testada de forma independente, pois só usa serviços verificados das camadas abaixo. O custo é que operações que cruzam muitas camadas acumulam overhead de chamada.

## Critério de projeto

Uma camada deve depender apenas de interfaces abaixo dela. Se dependências circulares surgem, a separação é apenas nominal — não há garantia arquitetural de isolamento.

## Relações (SPO)

- Sistema em Camadas → organiza → responsabilidades do [[Sistema Operacional]]
- Camada inferior → abstrai → hardware ou mecanismo básico
- Camada superior → usa → serviços da camada inferior
- Sistema THE → implementa → sistema em 6 camadas por Dijkstra (1968)
- MULTICS → generaliza → camadas como anéis concêntricos de privilégio
- Sistema em Camadas → contrasta com → [[Estrutura Monolítica de Sistema Operacional]]
- [[Micronúcleo]] → também busca → separação de responsabilidades
