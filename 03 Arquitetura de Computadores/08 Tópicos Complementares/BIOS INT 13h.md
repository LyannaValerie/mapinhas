---
title: BIOS INT 13h
aliases:
  - interrupção de disco BIOS
  - BIOS disk interrupt
  - serviços de disco BIOS
tags:
  - computação/arquitetura
date: 2026-04-24
source: "PC System Programming — Abacus, cap. 7"
codex_note: criado pelo Codex
---

# BIOS INT 13h

## Definição

`INT 13h` é o serviço de [[BIOS do IBM PC]] para acesso de baixo nível a discos, incluindo reset, leitura, escrita, verificação e consulta de parâmetros.

## Funções — Disquete (floppy)

| AH | Função | Registradores principais |
|---|---|---|
| 00h | Reset do sistema de disco | DL=drive |
| 01h | Ler status de disco | → AH=status |
| 02h | Ler setores | AL=n, BX=buf offset, CH=track, CL=setor, DH=lado, DL=drive, ES=buf seg |
| 03h | Escrever setores | igual ao 02h |
| 04h | Verificar setores | igual ao 02h (sem transferir dados) |
| 05h | Formatar trilha | ES:BX→ tabela de setores (4 bytes/setor) |
| 08h | Ler parâmetros do drive | → BL=tipo, CH=trilhas, CL=setores/trilha, DH=lados, DL=drives |
| 15h | Determinar tipo de drive (AT) | → AH: 0=ausente, 1=sem detecção de change, 2=com detecção, 3=HD |
| 16h | Detectar troca de disco (AT) | |
| 17h | Definir tipo de drive (AT) | AL=tipo |

## Funções — Hard disk (XT e AT)

| AH | Função | Observação |
|---|---|---|
| 00h | Reset do HD | |
| 01h | Ler status | → AH=código de erro |
| 02h | Ler setores | CH=cilindro, CL=setor, DH=cabeça, DL=80H/81H |
| 03h | Escrever setores | |
| 04h | Verificar setores | |
| 05h | Formatar trilha | |
| 08h | Ler parâmetros do HD | |
| 09h | Inicializar parâmetros do drive | |
| 0Ah | Ler long (setor + ECC) | |
| 0Bh | Escrever long | |
| 0Dh | Reset alternativo de disco | |
| 10h | Testar se drive está pronto | |
| 11h | Recalibrar drive | |
| 14h | Diagnóstico do controlador | |

## Endereçamento CHS

```
capacidade (bytes) = Heads × Cylinders × Sectors × 512
```

Bits de CL e CH codificam cilindro em 10 bits: bits 8–9 do cilindro ficam em bits 6–7 de CL (junto com o número do setor), permitindo endereçar até 1023 cilindros.

> [!note] DL para HD
> Primeiro HD = `80H`, segundo = `81H`. Drives de disquete usam `00H` e `01H`.

## Códigos de erro comuns (AH)

| Código | Erro |
|---|---|
| 01h | Função não disponível |
| 02h | Endereço não encontrado |
| 03h | Tentativa de escrita em disco protegido |
| 04h | Setor não encontrado |
| 08h | DMA overflow |
| 09h | Transmissão além do limite de segmento |
| 10h | Erro de leitura |
| 20h | Erro no controlador |
| 40h | Trilha não encontrada |
| 80h | Timeout — unidade não responde |

## Relações (SPO)

- [[BIOS INT 13h]] pertence a [[Serviços de BIOS do IBM PC]].
- [[BIOS INT 13h]] participa da [[Inicialização do IBM PC]] ao carregar setor de boot.
- [[BIOS INT 13h]] fornece acesso de bloco antes do sistema operacional assumir drivers próprios.

## Ver também

- [[BIOS do IBM PC]]
- [[Memória de Massa]]
- [[Disco Rígido]]
- [[Serviços de BIOS do IBM PC]]
