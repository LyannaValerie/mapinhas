---
title: Sistema de Arquivos do Windows NT
aliases:
  - NTFS
  - NT File System
  - sistema de arquivos NTFS
tags:
  - computação/sistemas
  - sistemas-operacionais/windows
  - computação/armazenamento
source: "Sistemas Operacionais Modernos, cap. 11"
created_by: Codex
---

# Sistema de Arquivos do Windows NT

## Definição

Sistema de arquivos padrão da família Windows NT. O Windows também suporta FAT-16 e FAT-32 para compatibilidade e mídia portátil, mas o NTFS é o sistema moderno.

## Comparação dos sistemas de arquivos

| FS | Endereços | Limite partição | Segurança | Uso |
|----|-----------|----------------|-----------|-----|
| FAT-16 | 16 bits | 2 GB | Não | Disquetes, legado |
| FAT-32 | 32 bits | 2 TB | Não | Mídias flash portáteis |
| NTFS | 64 bits | ~256 TB (prático) | Sim | Volumes de sistema Windows |

## MFT (Master File Table)

O NTFS organiza um volume inteiramente em torno da **MFT**: uma tabela onde cada entrada de 1 KB descreve um arquivo ou diretório. A própria MFT é um arquivo especial (`$MFT`).

### Arquivos de metadados do volume (exemplos)

| Nome | Conteúdo |
|------|---------|
| `$MFT` | a própria tabela de arquivos |
| `$MFTMirr` | backup das primeiras entradas da MFT |
| `$Bitmap` | mapa de clusters livres/usados |
| `$BadClus` | clusters defeituosos |
| `$Volume` | rótulo, versão, flags do volume |
| `$Extend` | extensões (journaling, quotas, links) |

## Atributos e streams

Cada arquivo na MFT é descrito por **atributos** (nome, dados, timestamps, ACL, index root, etc.). O atributo `$DATA` contém os dados do arquivo.

Um arquivo pode ter múltiplos atributos `$DATA` com nomes diferentes — isso cria **alternate data streams** (ADS): `arquivo.txt:nome_stream`.

## Integração com segurança e cache

- Cada arquivo armazena seu descritor de segurança como atributo na MFT, integrando com [[Segurança no Windows 8]].
- Todo acesso ao NTFS passa pelo [[Cache do Windows]] e pelo [[Entrada e Saída no Windows|gerenciador de E/S]].

## Relações (SPO)

- NTFS -> usa -> MFT
- MFT -> descreve -> arquivos e metadados do volume
- NTFS -> integra -> [[Segurança no Windows 8]]
- NTFS -> depende de -> [[Entrada e Saída no Windows]]
- NTFS -> armazena -> metadados como arquivos especiais

