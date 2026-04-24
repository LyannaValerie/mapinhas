---
title: Tipos de Dados ISA
aliases:
  - tipos de dados
  - data types
  - tipos numéricos
  - tipos não numéricos
  - BCD
  - mapa de bits
tags:
  - computação/arquitetura
date: 2026-04-13
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Tipos de Dados ISA

## Definição
Formatos de dado que o hardware da [[ISA]] reconhece e manipula diretamente com instruções dedicadas. Se o hardware suporta um tipo, os dados *devem* estar naquele formato — o programador não tem liberdade de escolher outro. Tipos sem suporte de hardware podem existir em software, sem formato obrigatório.

## Relações (SPO)
- Tipo com suporte de hardware → exige → formato específico fixado pela ISA
- Tipo sem suporte de hardware → pode ter → qualquer representação definida em software
- Inteiro → armazenado em → complemento de dois (padrão atual)
- Ponto flutuante → segue → padrão IEEE 754
- Ponteiro → é → endereço de máquina

---

## Tipos Numéricos

### Inteiro
- Comprimentos típicos: **8, 16, 32, 64 bits**
- Representação: **complemento de dois** (padrão universal)
- **Com sinal:** metade do intervalo negativa; bit mais significativo = sinal
- **Sem sinal:** intervalo dobrado do lado positivo (ex: 32 bits → 0 a 2³²−1)
- Uso: contagem, identificação, índices, aritmética geral

### Ponto Flutuante
- Comprimentos: **32 bits** (precisão simples), **64 bits** (dupla precisão), às vezes 128 bits
- Padrão: **IEEE 754** (quase universal desde os anos 1980)
- Maioria dos processadores tem registradores separados para FP (ver [[FPU]])
- Uso: cálculos científicos, gráficos, física, financeiro de alta precisão

### Decimal em Código Binário (BCD)
- Um dígito decimal (0–9) codificado em **4 bits** (nibble)
- Dois dígitos por byte → **BCD empacotado**
- Elimina conversão decimal↔binário para entrada/saída
- Requer instruções especiais de correção aritmética (DAA, DAS no x86)
- O bit auxiliar de vai-um (flag **A** no x86) existe exclusivamente para suportar BCD
- Uso: programas COBOL, sistemas financeiros legados

> [!warning] Bug do Y2K
> Programadores COBOL que representaram o ano com 2 dígitos BCD (8 bits = 100 valores possíveis) em vez de 4 dígitos — "economizando" bits — criaram o problema do ano 2000.

---

## Tipos Não Numéricos

### Caractere
- Códigos comuns: **ASCII** (7 bits, 1 byte com paridade), **Unicode** (múltiplos de 16 bits), **UTF-8** (1–4 bytes)
- Algumas ISAs têm instruções dedicadas para manipulação de cadeias (strings): cópia, busca, comparação

### Booleano
- Valores: verdadeiro / falso
- Na prática: **1 byte ou 1 palavra** por valor (bits individuais não têm endereço próprio)
- Convenção comum: `0 = falso`, qualquer outro valor = verdadeiro

### Mapa de Bits (Bitmap)
- Vetor de valores booleanos compactados: **1 bit por valor**
- Palavra de 32 bits → 32 booleanos
- Uso: monitorar blocos livres em disco, máscaras de permissão, atributos de pixel

### Ponteiro
- **Endereço de máquina** — referência para outra posição de memória
- Tamanho: igual ao barramento de endereços (32 ou 64 bits)
- Acesso a variáveis via ponteiro é extremamente comum (ver [[Modos de Endereçamento#Endereçamento Indireto de Registrador]])

> [!warning] Ponteiros e bugs
> Ponteiros são a principal fonte de erros graves em linguagens de baixo nível: acesso a memória inválida, dangling pointers, buffer overflow. Devem ser usados com cuidado extremo.

---

## Comparação entre Arquiteturas

### Tipos Numéricos Suportados em Hardware

| Tipo | [[Intel x86|Core i7]] | [[ARM]] OMAP4430 | [[AVR]] ATmega168 |
|---|---|---|---|
| Inteiro 8 bits com sinal | ✓ | ✓ | ✓ |
| Inteiro 16 bits com sinal | ✓ | ✓ | ✓ (pares de reg.) |
| Inteiro 32 bits com sinal | ✓ | ✓ | — |
| Inteiro 64 bits com sinal | ✓ (modo 64b) | — | — |
| Inteiro 8 bits sem sinal | ✓ | ✓ | ✓ |
| Inteiro 16 bits sem sinal | ✓ | ✓ | ✓ (pares de reg.) |
| Inteiro 32 bits sem sinal | ✓ | ✓ | — |
| BCD | ✓ | — | — |
| Ponto flutuante 32 bits | ✓ | ✓ (VFP) | — |
| Ponto flutuante 64 bits | ✓ | ✓ (VFP) | — |

### Características Distintivas

| Arquitetura | Destaque |
|---|---|
| **Core i7** | Suporte BCD para compatibilidade com COBOL; instruções de cadeia de caracteres nativas; múltiplos tamanhos de inteiro desde o 8 bits original |
| **ARM OMAP4430** | Carga/armazenamento especifica tamanho + extensão de sinal (`LDRSB`, `LDRH`...); converte para 32 bits automaticamente no registrador; sem suporte BCD |
| **AVR ATmega168** | Basicamente só byte de 8 bits; ponteiros de 16 bits via pares de registradores X/Y/Z; sem FP nativo; sem BCD |

> [!info] ARM e tipos pequenos
> O OMAP4430 é internamente uma máquina de 32 bits. Ao carregar um byte com sinal (`LDRSB`), o hardware estende automaticamente o bit de sinal para preencher os 32 bits do registrador de destino. Isso é mais elegante que o x86, que tem instrução separada por tamanho.

---

## Ver também
- [[ISA]]
- [[FPU]]
- [[Registradores x86]]
- [[ARM]]
- [[AVR]]
- [[Modos de Endereçamento]]
- [[Formatos de Instrução]]
