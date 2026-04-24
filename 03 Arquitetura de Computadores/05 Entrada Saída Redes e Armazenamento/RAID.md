---
title: RAID
aliases:
  - Redundant Arrays of Inexpensive Disks
  - RAID 0
  - RAID 1
  - RAID 5
  - striping
  - mirroring
  - parity disk
tags:
  - computação/arquitetura
  - sistemas
  - io
date: 2026-04-23
source: "P&H COD Cap. 8 §8.2"
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# RAID — Redundant Arrays of Inexpensive Disks

Organização de múltiplos discos pequenos para aumentar **desempenho** e/ou **confiabilidade** em relação a um único disco grande. O custo da redundância é diluído em disco baratos.

## Níveis de RAID

| Nível | Nome | Discos extras | Uso prático |
|---|---|---|---|
| **RAID 0** | Striping | 0 | Vídeo editing; sem confiabilidade |
| **RAID 1** | Mirroring | N (dobra total) | Alta confiabilidade, custo alto |
| **RAID 2** | ECC por bit | variável | Obsoleto |
| **RAID 3** | Paridade bit-interleaved | 1 | Nicho; Storage Concepts |
| **RAID 4** | Paridade block-interleaved | 1 | Network Appliance (gargalo em write) |
| **RAID 5** | Paridade distribuída | 1 | Mais usado; elimina gargalo do RAID 4 |
| **RAID 6** | Paridade dupla | 2 | Sobrevive 2 falhas simultâneas |

## Detalhes por Nível

### RAID 0 — Striping
- Blocos logicamente sequenciais distribuídos em múltiplos discos
- Aparece ao SO como um único disco grande
- **Sem redundância** — uma falha perde todos os dados
- Melhora throughput em acessos grandes (paralelismo de cabeças)

### RAID 1 — Mirroring (Shadowing)
- Cada escrita vai para 2 discos (disco + espelho)
- Leitura pode vir de qualquer dos dois (melhora throughput de leitura)
- Falha de um disco: sistema usa o espelho sem interrupção
- **Custo:** 2× o número de discos; solução mais cara

### RAID 2 — Error Correction Code
- Esquema ECC aplicado em nível de bit (como memória ECC)
- **Caído em desuso;** não descrito em detalhes aqui

### RAID 3 — Bit-Interleaved Parity
- 1 disco de paridade dedicado
- Paridade calculada bit a bit sobre os dados
- Acesso a qualquer dado envolve todos os discos → alto throughput sequencial
- Nicho: streams de dados contínuos

### RAID 4 — Block-Interleaved Parity
- 1 disco de paridade dedicado; dados em blocos (não bits)
- Leituras independentes (cada disco pode servir uma leitura diferente)
- **Gargalo:** toda escrita atualiza o disco de paridade → disco de paridade é bottleneck

### RAID 5 — Distributed Block-Interleaved Parity
- Paridade distribuída entre todos os discos (sem disco de paridade dedicado)
- Elimina o gargalo do RAID 4
- **Nível mais amplamente usado em produção**
- Tolera falha de 1 disco; reconstrução usa paridade + dados restantes

### RAID 6 — Double Parity
- Dois blocos de paridade independentes distribuídos
- Tolera falha simultânea de **2 discos**
- Uso crescente em arrays grandes (probabilidade de segunda falha durante rebuild)

## Impacto na Confiabilidade (MTTF)

Para N discos em RAID 0 (sem redundância):
$$\text{MTTF}_\text{array} = \frac{\text{MTTF}_\text{disk}}{N}$$

Exemplo: MTTF\_disk = 500.000 h, N = 10 → MTTF\_array = 50.000 h.

RAID 1 com 2 discos aumenta MTTF porque após falha de um disco, o sistema continua operando. O MTTF do par aumenta substancialmente.

> [!note] MTTF de campo vs. fabricante
> Fabricantes citam MTTF de 600k–1M horas. Estudos reais medem AFR de 3–7%. Ver [[Confiabilidade e Disponibilidade]].

## Relações (SPO)

- RAID → combina → múltiplos [[Disco Magnético|discos]] para redundância ou desempenho
- RAID 0 → usa → striping (sem redundância)
- RAID 1 → usa → mirroring (escrita duplicada)
- RAID 5 → distribui → paridade entre todos os discos
- RAID → aumenta → [[Confiabilidade e Disponibilidade]] do subsistema de armazenamento
- [[Teoria de Filas para IO]] → modela → impacto de múltiplos discos RAID na latência

## Ver também

- [[Disco Magnético]]
- [[Confiabilidade e Disponibilidade]]
- [[Hierarquia de Memória]]
- [[Teoria de Filas para IO]]
