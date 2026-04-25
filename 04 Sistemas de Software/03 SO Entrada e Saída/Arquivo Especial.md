---
title: Arquivo Especial
aliases:
  - special file
  - arquivo especial de bloco
  - arquivo especial de caractere
  - block special file
  - character special file
tags:
  - computação/sistemas
  - computação/armazenamento
source: "Sistemas Operacionais Modernos, cap. 1"
created_by: Codex
date: 2026-04-25
---

%% criado pelo Codex %%

# Arquivo Especial

## Definição

Arquivo que representa um dispositivo ou interface do kernel dentro do namespace do sistema de arquivos.

## Relações (SPO)

- Arquivo Especial -> representa -> dispositivo
- Arquivo especial de bloco -> modela -> dispositivo endereçável por blocos
- Arquivo especial de caractere -> modela -> fluxo de caracteres
- `/dev` -> contém -> arquivos especiais em sistemas UNIX
- [[Dispositivo de Bloco]] -> corresponde a -> arquivo especial de bloco
- [[Dispositivo de Caractere]] -> corresponde a -> arquivo especial de caractere

## Papel técnico

Arquivos especiais permitem usar operações de arquivo para acessar dispositivos. A interface fica uniforme, mas o comportamento real é implementado pelo driver do dispositivo.

## Diferença central

Dispositivos de bloco permitem acesso por blocos endereçáveis; dispositivos de caractere expõem fluxo sequencial de bytes ou caracteres.
