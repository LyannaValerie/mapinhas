---
title: Monitor de Máquina Virtual
aliases:
  - VMM
  - virtual machine monitor
  - hipervisor
tags:
  - computação/sistemas
source: "Sistemas Operacionais Modernos, cap. 1"
created_by: Codex
date: 2026-04-25
---

%% criado pelo Codex %%

# Monitor de Máquina Virtual

## Definição

Camada de software que cria e controla [[Máquina Virtual|máquinas virtuais]], multiplexando hardware físico entre sistemas operacionais convidados. Também chamado **hipervisor**.

## Mecanismo

O VMM apresenta a cada SO convidado uma cópia virtual da máquina física. Quando o convidado tenta executar instruções privilegiadas (que requerem [[Modo Kernel e Modo Usuário|modo núcleo]] real), o hardware gera uma armadilha (trap) para o VMM, que simula o efeito da instrução e devolve o controle ao convidado — técnica chamada **trap-and-emulate**.

## Tipo 1 vs Tipo 2

| Tipo | Execução | Exemplo |
|---|---|---|
| **Tipo 1** (bare-metal) | Executa diretamente sobre o hardware físico, sem SO hospedeiro | VMware ESXi, IBM VM/370, Xen |
| **Tipo 2** (hospedado) | Executa como processo sobre um SO hospedeiro convencional | VirtualBox, VMware Workstation |

No Tipo 1, o VMM é o próprio SO do hardware. No Tipo 2, há um SO adicional abaixo do VMM, o que adiciona latência mas simplifica a instalação.

## Contexto histórico

O conceito surgiu com o **IBM VM/370** nos anos 1960–70. Cada usuário recebia uma cópia virtual do IBM 370. O SO convidado padrão era o **CMS** (Conversational Monitor System), um SO de um único usuário simples, que rodava sobre a VM. Isso permitiu tempo compartilhado sem modificar o CMS.

## Diferença em relação ao Exonúcleo

O VMM clona a máquina real (cada VM recebe uma imagem completa do hardware). O [[Exonúcleo]] divide a máquina (cada VM recebe um subconjunto de recursos físicos reais), eliminando a camada de remapeamento.

## Relações (SPO)

- Monitor de Máquina Virtual → cria → [[Máquina Virtual]]
- Monitor de Máquina Virtual → multiplexa → hardware físico entre convidados
- Monitor de Máquina Virtual → isola → sistemas convidados entre si
- Monitor de Máquina Virtual → também chamado → hipervisor
- Tipo 1 VMM → executa sobre → hardware físico diretamente
- Tipo 2 VMM → executa sobre → sistema operacional hospedeiro
- [[Sistema Operacional]] convidado → executa sobre → Monitor de Máquina Virtual
- VMM → usa → trap-and-emulate para instruções privilegiadas
- VMM → contrasta com → [[Exonúcleo]]

