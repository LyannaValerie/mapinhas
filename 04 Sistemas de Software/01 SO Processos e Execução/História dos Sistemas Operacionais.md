---
title: História dos Sistemas Operacionais
aliases:
  - evolução dos sistemas operacionais
  - gerações de sistemas operacionais
tags:
  - computação/sistemas
source: "Sistemas Operacionais Modernos, cap. 1"
created_by: Codex
date: 2026-04-25
---

%% criado pelo Codex %%

# História dos Sistemas Operacionais

## Definição

Linha evolutiva dos sistemas operacionais, da execução manual em máquinas sem SO até sistemas multiprogramados, de tempo compartilhado, pessoais e móveis (Tanenbaum §1.2).

## Primeira geração (1945–1955): válvulas

Sem sistema operacional. Cada programador reservava tempo, inseria seu painel de programação e operava a máquina diretamente. Programação em código de máquina absoluto ou por ligação de cabos em painéis. Máquinas: ENIAC, Z3, Atlas (Manchester).

Não havia separação entre projetista, programador e operador.

## Segunda geração (1955–1965): transistores e sistemas em lote

Transistores tornaram os computadores confiáveis o suficiente para vender a clientes. Surgiu separação de papéis: projetistas, operadores, programadores. Os mainframes ficavam em salas climatizadas.

O [[Sistema em Lote]] surgiu para maximizar utilização. Fluxo típico: cartões → IBM 1401 (E/S) → fita → IBM 7094 (cálculo) → fita de saída → 1401 (impressão). Linguagem de controle: FMS (Fortran Monitor System).

## Terceira geração (1965–1980): CIs e multiprogramação

O **IBM 360** (1964) foi o primeiro grande sistema com circuitos integrados (CIs) de pequena escala. Introduziu a ideia de *família de computadores compatíveis* — programas rodavam em qualquer máquina da linha.

Principais avanços:
- **Multiprogramação**: múltiplos jobs na memória simultaneamente; quando um bloqueia em E/S, outro executa a CPU
- **Spooling**: saída gravada em disco e impressa assincronamente
- **Tempo compartilhado**: CTSS (MIT, 1961) e MULTICS (MIT + Bell Labs + GE)
- **UNIX** (Bell Labs, 1969): derivado do MULTICS, mais simples e portável, escrito em C

## Quarta geração (1980–presente): computadores pessoais

Microprocessadores (Intel 8080, depois 8086) tornaram o computador acessível individualmente. CP/M foi o primeiro SO dominante de PC; substituído pelo MS-DOS após IBM PC (1981). Apple Macintosh (1984) popularizou GUI.

Windows (1985–presente) dominou o mercado de desktop. Apple adotou núcleo derivado do Mach no macOS X (1999). Linux (Linus Torvalds, 1991) consolidou-se como SO de servidores e dispositivos embarcados.

## Quinta geração (1990–presente): computadores móveis

Smartphones com Symbian, BlackBerry OS, depois iOS (Apple, 2007) e Android (Google, baseado em Linux). Computação distribuída em nuvem deslocou o foco de hardware local para serviços remotos.

## Marcos condensados

| Período | Tecnologia | Marcos |
|---|---|---|
| 1945–1955 | Válvulas | ENIAC; sem SO; painéis de programação |
| 1955–1965 | Transistores | IBM 7094; FMS; [[Sistema em Lote]] |
| 1965–1980 | CIs | IBM 360; multiprogramação; CTSS; MULTICS; UNIX |
| 1980–presente | Microprocessadores | CP/M; MS-DOS; Mac; Windows; Linux |
| 1990–presente | Smartphones | iOS; Android; computação em nuvem |

## Relações (SPO)

- Primeira geração → usava → válvulas e painéis de conexão manuais
- Segunda geração → introduziu → [[Sistema em Lote]] e IBM 7094
- Terceira geração → consolidou → multiprogramação e tempo compartilhado
- IBM 360 → introduziu → família de computadores compatíveis com CIs
- CTSS e MULTICS → desenvolvem → conceito de [[Sistema de Tempo Compartilhado]]
- UNIX → deriva de → MULTICS; base para Linux e macOS
- Linux → deriva de → UNIX e MINIX
- [[História do UNIX e do Linux]] → detalha → ramificação UNIX/BSD/Linux
