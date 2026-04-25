---
title: BIOS do IBM PC
aliases:
  - PC BIOS
  - BIOS do PC
  - Basic Input Output System do IBM PC
tags:
  - computação/arquitetura
date: 2026-04-24
created_by: Codex
source: desconhecida
---

# BIOS do IBM PC

Nota criada pelo Codex.

## Definição

BIOS do IBM PC é o firmware residente em ROM que fornece rotinas básicas para inicialização e acesso padronizado a dispositivos do PC.

No PC clássico, parte do espaço de endereçamento é reservado para ROM. O segmento alto contém rotinas de BIOS, carregador do sistema e, em muitas máquinas, ROM BASIC. Extensões de hardware também podem instalar rotinas em áreas de ROM reservadas.

## Relações (SPO)
- BIOS → reside em → [[ROM — Tipos|ROM]]
- BIOS → fornece → serviços por interrupção
- BIOS → inicializa → hardware básico
- BIOS → participa de → boot do sistema
- Extensões de hardware → podem expor → ROM de expansão
- [[MS-DOS]] → depende de → serviços de baixo nível do BIOS

---

## Papel no PC

O BIOS reduz acoplamento entre programa e dispositivo físico: programas chamam serviços por interrupções, enquanto detalhes de controlador, teclado, vídeo, disco e temporização ficam encapsulados em rotinas de firmware.

Isso não torna o BIOS um sistema operacional completo. Ele fornece primitivas de máquina; o gerenciamento de arquivos, processos de programa e políticas de execução ficam no [[MS-DOS]] ou em outro sistema carregado depois do boot.

## Ver também
- [[Firmware]]
- [[ROM — Tipos]]
- [[Inicialização do IBM PC]]
- [[Serviços de BIOS do IBM PC]]
- [[BIOS INT 10h]]
- [[BIOS INT 13h]]
- [[BIOS INT 16h]]
- [[BIOS INT 17h]]
- [[BIOS Porta Serial]]
- [[BIOS Data e Hora]]
- [[BIOS Configuração do Sistema]]
- [[Variáveis BIOS]]
- [[Tabela de Vetores de Interrupção]]
- [[Interrupção]]
- [[MS-DOS]]
