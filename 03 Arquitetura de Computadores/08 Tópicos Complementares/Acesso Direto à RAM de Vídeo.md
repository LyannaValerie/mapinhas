---
title: Acesso Direto à RAM de Vídeo
aliases:
  - direct video RAM access
  - acesso direto à memória de vídeo
tags:
  - computação/arquitetura
  - computação/baixo-nível
date: 2026-04-24
source: "PC System Programming — Abacus, cap. 10"
codex_note: criado pelo Codex
---

# Acesso Direto à RAM de Vídeo

## Definição

Acesso direto à RAM de vídeo é a escrita de caracteres e atributos diretamente no segmento de memória da placa de vídeo, sem passar por rotinas de saída de linguagem, [[BIOS INT 10h]] ou funções do [[MS-DOS]].

## Endereçamento

- Placa monocromática: registrador de endereço do CRTC em `3B4h`; RAM de vídeo em `B000h`.
- Placa colorida: registrador de endereço do CRTC em `3D4h`; RAM de vídeo em `B800h`.
- A variável BIOS `CRT_START`, em `0040:004E`, indica o deslocamento da página de vídeo visível.

## Modelo de escrita

Em modo texto, cada célula da tela é um par `ASCII/atributo` (estrutura VELB — 2 bytes por célula). Uma tela de 80×25 = 2000 células.

Endereço lógico da célula:

```text
base_da_pagina_visivel + (linha * 80 + coluna) * 2
```

A variável BIOS `CRT_START` em `0040:004Eh` contém o deslocamento em **bytes** da página ativa. Ao somar ao ponteiro base, o resultado aponta para o início da página visível.

## Estrutura VELB (par ASCII/atributo)

```c
typedef struct {
    unsigned char ascii;      /* caractere ASCII */
    unsigned char attribute;  /* cor frente, cor fundo, piscar */
} VELB;

typedef VELB far *VP;         /* ponteiro FAR obrigatório */
```

O ponteiro deve ser `FAR` em modelos de memória pequenos (small/medium) porque a RAM de vídeo está em segmento diferente do segmento de dados do programa.

## Inicialização em C

```c
VP vptr;   /* ponteiro global para RAM de vídeo */

void init_dprint(void) {
    unsigned int crtc_addr;
    /* Detectar tipo de placa pelo registrador CRTC */
    crtc_addr = *(unsigned int far *)0x00400063L; /* 3B4h=mono, 3D4h=color */
    if (crtc_addr == 0x3B4)
        vptr = (VP)0xB0000000L;   /* placa monocromática */
    else
        vptr = (VP)0xB8000000L;   /* placa colorida */
}
```

## Por que mais rápido que INT 10h ou DOS

Chamadas ao BIOS (INT 10h) ou ao DOS (INT 21h) têm overhead de interrupção + lógica interna. Escrever diretamente na RAM de vídeo elimina esse overhead — cada caractere é uma operação de escrita em memória.

## Relações (SPO)

- [[Acesso Direto à RAM de Vídeo]] usa [[Variáveis BIOS]].
- [[Acesso Direto à RAM de Vídeo]] evita chamadas de [[BIOS INT 10h]].
- [[Acesso Direto à RAM de Vídeo]] depende de segmentação em modo real.
- [[Acesso Direto à RAM de Vídeo]] pode interferir na [[Interface de Mouse DOS]] quando sobrescreve o ponteiro.

## Ver também

- [[BIOS INT 10h]]
- [[Variáveis BIOS]]
- [[MS-DOS]]
- [[Interface de Mouse DOS]]
