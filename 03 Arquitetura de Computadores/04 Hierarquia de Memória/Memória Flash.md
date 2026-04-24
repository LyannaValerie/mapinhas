---
title: Memória Flash
aliases:
  - flash
  - NAND
  - NOR flash
  - SLC
  - MLC
  - TLC
  - QLC
  - memória não-volátil
tags:
  - computação/hardware
  - computação/arquitetura
date: 2026-04-12
source: "Livro Hardware clubedohardware.com.br — Cap. 35"
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Memória Flash

Memória não-volátil baseada em transistores MOSFET com **porta flutuante**.  
Dois tipos: **NOR** (acesso aleatório, execução direta, ROM) e **NAND** (acesso sequencial em blocos, armazenamento de massa).

→ ROM de placa-mãe usa NOR: [[ROM — Tipos]]  
→ SSDs e pen drives usam NAND: [[Memória de Massa]]  
→ Posição na hierarquia: [[Hierarquia de Memória]] (L5/armazenamento)

---

## Relações (SPO)

- Flash → armazena bits em → porta flutuante (transistor MOSFET)
- NOR → organiza células em → paralelo (acesso aleatório)
- NAND → organiza células em → série (acesso por página)
- NOR → permite → execução de código direta (sem cópia para RAM)
- NAND → requer → cópia para RAM antes de execução
- Flash → limita → ciclos de apagamento/escrita (degradação de óxido)

---

## Arquitetura do Transistor

MOSFET convencional: 3 terminais — **dreno** (drain), **fonte** (source), **porta** (gate).  
Flash adiciona uma **porta flutuante** entre a porta de controle e o canal:

```
[Porta de controle]
      ↓
[Porta flutuante] ← armazena carga elétrica (isolada por óxido)
      ↓
[Canal: fonte → dreno]
```

- Porta flutuante carregada → campo elétrico cancela parte da porta de controle → transistor precisa de mais tensão para conduzir → representa bit "1"
- Porta flutuante descarregada → transistor conduz normalmente com tensão de disparo → representa bit "0"
- Carga retida por **anos** (sem alimentação)

---

## Bits por Célula

| Tipo | Bits/célula | Estados | Velocidade | Vida útil | Custo relativo |
|---|---|---|---|---|---|
| **SLC** | 1 | 2 | Máxima | 100.000 ciclos | Alto |
| **MLC** | 2 | 4 | Alta | 30.000 ciclos | Médio |
| **TLC** | 3 | 8 | Média | 3.000 ciclos | Baixo |
| **QLC** | 4 | 16 | Baixa | 1.000 ciclos | Muito baixo |
| **PLC** | 5 | 32 | Estimada | (em pesquisa) | — |

**Trade-off:** mais bits/célula → maior capacidade + menor custo, mas menor velocidade + menor vida útil + maior consumo.

### Por que mais bits = mais lento?
- SLC: apenas verifica se transistor **conduz ou não** (limiar binário)
- MLC+: mede **quantidade de corrente** (mais estados → mais imprecisão → mais erros de leitura → releituras)
- Gravação: ajustar quantidade exata de carga é mais demorado com mais estados
- Mais erros → mais correção ECC → latência adicional

---

## NOR Flash

Células ligadas em **paralelo** (similar à porta lógica NOR).

```
BL0   BL1   BLn
 |     |     |
[T]   [T]   [T]  ← WL0
 |     |     |
[T]   [T]   [T]  ← WL1
 |     |     |
GND   GND   GND
```

### Características

| Aspecto | NOR |
|---|---|
| Acesso | Aleatório (endereço → dado direto) |
| Interface | Barramento de endereços + dados (como RAM) |
| Execução direta | Sim (XiP — eXecute in Place) |
| Leitura aleatória | Rápida |
| Leitura sequencial | Lenta |
| Densidade | Menor (célula maior) |
| Apagamento | Lento |

### Usos
- **ROM de placa-mãe** (Flash-ROM = NOR): firmware UEFI/BIOS
- Armazenamento de código executável em **telefones celulares** (legado)

---

## NAND Flash

Células ligadas em **série** por coluna, formando **páginas** (similar à porta lógica NAND).

```
BL0   BL1   BLn
 |     |     |
BLS  BLS   BLS   ← seletor de Bit Line
 |     |     |
[T]   [T]   [T]  ← WL0
[T]   [T]   [T]  ← WL1
[T]   [T]   [T]  ← WLn
 |     |     |
GS    GS    GS   ← seletor de terra (Ground Select)
 |     |     |
GND   GND   GND
```

### Características

| Aspecto | NAND |
|---|---|
| Acesso | Por bloco/página |
| Interface | Não compatível com barramento de CPU |
| Execução direta | Não (cópia obrigatória para RAM) |
| Leitura sequencial | Rápida |
| Leitura aleatória | Lenta |
| Densidade | Maior (célula ~60% menor que NOR) |
| Unidade mínima de apagamento | Bloco (4 KiB a 512 KiB; várias páginas) |

### Estrutura de acesso
- **Página**: grupo de células em série em uma coluna (menor unidade de leitura/escrita)
- **Bloco**: conjunto de páginas (menor unidade de **apagamento**)
- Cada bloco inclui área de **ECC** (código de correção de erros)

### Usos
- **SSDs**, **pen drives**, **cartões de memória** (SD, microSD)
- Armazenamento de arquivos em smartphones
- Sistema operacional vê NAND como "disco rígido" (mediado por controlador)

---

## NOR vs NAND — Comparativo

| Característica | NOR | NAND |
|---|---|---|
| Organização | Paralelo | Série |
| Acesso aleatório | Melhor | Pior |
| Acesso sequencial | Pior | Melhor |
| Densidade | Menor | Maior |
| Interface | Como RAM | Como disco |
| Execução direta | Sim | Não |

---

## Vida Útil (P/E Cycles)

Limite de **ciclos de apagamento e escrita** (P/E cycles). Causa: degradação do óxido isolante da porta flutuante a cada apagamento.

| Tipo | Ciclos típicos |
|---|---|
| SLC | 100.000 |
| MLC | 30.000 |
| TLC | 3.000 |
| QLC | 1.000 |

> Leitura não tem limite de ciclos — apenas apagamento/escrita degrada.  
> SSDs usam **wear leveling** para distribuir escritas e maximizar vida útil — ver [[SSD]] (Cap. 48).

---

## Tempo de Retenção

Causa de perda: descarga natural das portas flutuantes (independente de P/E cycles).

- Estimativa conservadora de fabricantes: **≥ 100 anos** a 25 °C
- Cálculos teóricos: centenas a milhares de anos
- Temperaturas elevadas aceleram descarga, mas tempo ainda muito acima de 100 anos
- **Usuário comum não precisa se preocupar** com dados "sumindo" por falta de uso

---

## Conexões com Outras Notas

- [[ROM — Tipos]] — Flash-ROM = NOR; EEPROM precursora da flash
- [[Memória de Massa]] — NAND = armazenamento secundário (SSD, pen drive, cartão)
- [[Hierarquia de Memória]] — flash ocupa L5 (abaixo de DRAM, acima de armazenamento remoto)
- [[RAM — Tecnologias e Módulos]] — DRAM volátil vs flash não-volátil; ambas baseadas em transistor (MOSFET)
- [[Firmware]] — BIOS/UEFI gravado em NOR flash (Flash-ROM)
- [[Memória]] — comparativo RAM (DRAM, volátil) vs flash (não-volátil)
