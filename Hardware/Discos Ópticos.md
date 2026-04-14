---
title: Discos Ópticos
aliases:
  - CD-ROM
  - CD
  - DVD
  - Blu-ray
  - CD-R
  - CD-RW
  - disco compacto
  - disco óptico
  - Compact Disc
  - pit
  - land
  - depressão
  - plano
tags:
  - computação/fundamentos
  - computação/armazenamento
date: 2026-04-12
source: "Organização Estruturada de Computadores — Tanenbaum, Cap. 2"
---

# Discos Ópticos

Mídia de armazenamento que usa laser para ler/gravar dados por diferença de refletividade. Substrato: policarbonato com **depressões** (pits) e **planos** (lands) lidos por fotodetector.

**Princípio de leitura:** depressão tem altura de ¼ do comprimento de onda → luz refletida da depressão chega com defasagem de ½ comprimento de onda → interferência destrutiva → menos luz → sinal diferente do plano. Transição plano↔depressão = bit 1; ausência de transição = bit 0.

## Relações (SPO)
- CD-ROM → armazena → 650–700 MB; velocidade 1x = 150 KB/s
- DVD → usa → laser vermelho; 7× capacidade do CD
- Blu-ray → usa → laser azul; ~25 GB por face
- CD-R → usa → camada de corante; gravação única por queima química
- CD-RW → usa → liga metálica de dois estados; regravável

## CD (Compact Disc)

Desenvolvido pela Philips + Sony (1980). Padrão: **Red Book** (IS 10149). 120 mm de diâmetro, 1,2 mm de espessura. Trilha espiral do centro para a borda: 22.188 voltas, ~5,6 km desenrolada. Velocidade linear constante (~120 cm/s) → rotação variável: 530 RPM (borda interna) → 200 RPM (borda externa).

### CD-ROM (Yellow Book, 1984)
Adaptação do CD para dados de computador. Mesmo formato físico. Três camadas de ECC.

#### Estrutura de dados

| Nível | Tamanho |
|---|---|
| Símbolo | 14 bits (mapeia 1 byte via tabela) |
| Quadro | 42 símbolos → 24 bytes de dados úteis |
| Setor (modo 1) | 98 quadros = 2.048 bytes dados + 16 bytes preâmbulo + 288 bytes ECC |

- **Eficiência:** 98 × 42 × 14 bits = 7.203 bytes → transportam 2.048 bytes úteis = **28%**
- **Velocidade 1x:** 75 setores/s × 2.048 bytes = **153.600 bytes/s**
- **Capacidade:** ~650 MB (padrão) / ~700 MB (360 mil setores, extensão posterior)

> [!note] Modo 2
> Combina campos de dados + ECC em 2.336 bytes sem correção de setor. Usado para áudio/vídeo onde pequenos erros são toleráveis.

#### Sistema de arquivos: IS 9660 (High Sierra)
- **Nível 1:** nomes 8+3 caracteres, maiúsculas, máx. 8 diretórios aninhados — compatível com qualquer SO
- **Nível 2:** nomes até 32 caracteres
- **Nível 3:** arquivos não contíguos
- **Rock Ridge:** extensões para Unix (nomes longos, UIDs, symlinks)

### CD-R (Orange Book, 1989)
Gravável **uma vez**. Camada de corante orgânico (cianina — verde, ou ftalocianina — amarelo-laranja) entre policarbonato e refletor.

- **Escrita:** laser ~12 mW aquece e rompe ligações do corante → ponto escuro permanente
- **Leitura:** laser 0,5 mW detecta diferença entre corante intacto (refletivo) e queimado (escuro)
- **Gravação incremental:** suporta múltiplas sessões, cada uma com VTOC própria. SO lê VTOC mais recente.

### CD-RW
Regravável. Liga de prata-índio-antimônio-telúrio com dois estados estáveis:

| Estado | Refletividade | Representa |
|---|---|---|
| Cristalino | Alta | Plano |
| Amorfo | Baixa | Depressão |

Três potências de laser: alta (funde → amorfo), média (recristaliza → plano), baixa (lê sem alterar).

## DVD (Digital Versatile Disc)

Três melhorias sobre o CD → capacidade 7× maior:

| Parâmetro | CD | DVD |
|---|---|---|
| Tamanho da depressão | 0,8 µm | 0,4 µm |
| Espaçamento de trilhas | 1,6 µm | 0,74 µm |
| Comprimento de onda do laser | 0,78 µm (infravermelho) | 0,65 µm (vermelho) |

### Formatos

| Formato | Capacidade |
|---|---|
| 1 face, 1 camada | 4,7 GB |
| 1 face, 2 camadas | 8,5 GB |
| 2 faces, 1 camada | 9,4 GB |
| 2 faces, 2 camadas | 17 GB |

- **Velocidade 1x:** 1,4 MB/s
- **Dupla camada:** refletor de alumínio (inferior) + refletor semitransparente (superior); laser foca em cada um independentemente
- **Capacidade prática:** com MPEG-2, 4,7 GB suporta 133 min de vídeo 720×480 com até 8 trilhas de áudio

> [!note] Incompatibilidade intencional por região
> Hollywood exigiu codificação regional (EUA, Europa, etc.) para controlar janelas de lançamento. Motivação comercial, não técnica.

## Blu-ray

Laser azul (405 nm) → comprimento de onda mais curto → foco mais preciso → depressões e planos menores → maior densidade.

| | Blu-ray | DVD | CD |
|---|---|---|---|
| Laser | Azul (405 nm) | Vermelho (650 nm) | Infravermelho (780 nm) |
| Capacidade 1 face | 25 GB | 4,7 GB | 0,65 GB |
| Capacidade 2 faces | 50 GB | 9,4 GB | — |
| Taxa de dados 1x | ~4,5 MB/s | 1,4 MB/s | 150 KB/s |

Substituto do DVD; reprodutores Blu-ray leem DVDs e CDs com segundo laser.

## Comparação de Desempenho

> [!warning] Discos ópticos vs. discos magnéticos
> Mesmo um CD-ROM 32x (~4,9 MB/s) é bem mais lento que um HD SCSI (~10–640 MB/s). Tempo de busca em CD-ROM chega a centenas de ms (vs. 5–10 ms em HD). Uso adequado: distribuição e arquivamento, não armazenamento ativo.

## Ver também
- [[Memória de Massa]] — discos ópticos como tipo de armazenamento persistente
- [[SSD]] — alternativa eletrônica de alta velocidade
- [[Disco Rígido]] — comparação de geometria e desempenho
- [[RAID]] — organização redundante de discos
