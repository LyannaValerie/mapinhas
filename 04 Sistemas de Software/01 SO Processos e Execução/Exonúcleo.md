---
title: Exonúcleo
aliases:
  - exokernel
  - exokernels
tags:
  - computação/sistemas
source: "Sistemas Operacionais Modernos, cap. 1"
created_by: Codex
date: 2026-04-25
---

%% criado pelo Codex %%

# Exonúcleo

## Definição

Arquitetura de sistema operacional em que o kernel (exonúcleo) protege e multiplexa recursos físicos, mas **não impõe abstrações de alto nível**. A separação entre proteção e abstração é o princípio central.

Proposta por ENGLER et al. (1995) no MIT como alternativa ao modelo de [[Monitor de Máquina Virtual|VMM]] e ao modelo tradicional de [[Sistema Operacional como Máquina Estendida|máquina estendida]].

## Modelo de recursos

O exonúcleo divide fisicamente os recursos entre máquinas virtuais, sem remapeamento:
- VM 1 recebe blocos de disco 0–1023
- VM 2 recebe blocos de disco 1024–2047
- Cada VM tem uma fração real de CPU, memória e outros recursos

O exonúcleo **verifica** se uma VM está tentando usar recursos de outra. Se não, deixa passar. Se sim, bloqueia. Não há camada de tradução de endereços.

## Contraste com VMM

| Aspecto | VMM (tipo 1) | Exonúcleo |
|---|---|---|
| Recursos | Cada VM vê cópia virtual completa | Cada VM recebe fração física real |
| Remapeamento | Sim — VMM mantém tabelas de tradução | Não — exonúcleo apenas rastreia proprietário |
| Overhead | Camada de tradução em toda operação | Apenas verificação de propriedade |
| Flexibilidade | SO convidado inteiro; interface padronizada | Biblioteca pode implementar qualquer abstração |

## Papel técnico

O exonúcleo separa dois papéis que sistemas tradicionais fundem:
1. **Proteção** — garantir que processos não acessem recursos alheios (feito pelo exonúcleo em modo kernel)
2. **Abstração** — transformar recursos em arquivos, sockets, memória virtual (feito por bibliotecas em modo usuário)

Aplicações que precisam de política especializada (e.g., banco de dados com controle fino de buffer pool) podem implementar suas próprias bibliotecas sem depender das abstrações padrão do SO.

## Trade-off

O modelo pode aumentar desempenho e flexibilidade para aplicações especializadas, mas desloca complexidade para bibliotecas e complica a compatibilidade entre diferentes políticas.

## Relações (SPO)

- Exonúcleo → expõe → recursos físicos particionados (não abstraídos)
- Exonúcleo → verifica → que cada VM usa apenas seus próprios recursos
- Exonúcleo → delega → abstração para bibliotecas em espaço de usuário
- Biblioteca de sistema → implementa → política de abstração sobre recursos reais
- Exonúcleo → elimina → camada de remapeamento do VMM
- Exonúcleo → contrasta com → [[Monitor de Máquina Virtual]]
- Exonúcleo → contrasta com → [[Sistema Operacional como Máquina Estendida]]
- ENGLER et al. (1995) → propõe → exonúcleo no MIT
