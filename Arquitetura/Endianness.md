---
title: Endianness
aliases:
  - big endian
  - little endian
  - ordenação de bytes
  - byte order
  - big-endian
  - little-endian
  - network byte order
tags:
  - computação/arquitetura
date: 2026-04-12
source: "Organização Estruturada de Computadores — Tanenbaum, Cap. 2"
---

# Endianness

## Definição
Ordenação dos bytes de uma palavra multibyte na [[Memória]]. Define qual byte ocupa o endereço mais baixo da palavra.

## Relações (SPO)
- Big endian → armazena → byte mais significativo no menor endereço
- Little endian → armazena → byte menos significativo no menor endereço
- TCP/IP → usa → big endian como "network byte order"
- x86/x64 → usa → little endian
- Conversão de endianness → necessária → ao trocar dados binários entre arquiteturas diferentes

## Dois Modelos

### Big Endian
Byte de **maior ordem** (mais significativo, MSB) no endereço mais baixo. Leitura "natural" da esquerda para a direita.

```
Palavra de 32 bits com valor 0x01020304:
Endereço:  0     1     2     3
Byte:     0x01  0x02  0x03  0x04
           MSB                LSB
```

Usado por: SPARC, IBM mainframes, Motorola 68000, TCP/IP.

### Little Endian
Byte de **menor ordem** (menos significativo, LSB) no endereço mais baixo.

```
Palavra de 32 bits com valor 0x01020304:
Endereço:  0     1     2     3
Byte:     0x04  0x03  0x02  0x01
           LSB                MSB
```

Usado por: Intel x86/x64, ARM (configurável), RISC-V.

## Problema de Interoperabilidade

Para texto puro (ASCII/UTF-8), endianness não importa — cada caractere ocupa exatamente 1 byte. Para inteiros multibyte, a diferença é crítica.

> [!example] Transferência big endian → little endian
> Registro com: string "Jim Smith" + inteiro idade=21 + inteiro depto=260
>
> Transmissão byte-a-byte (byte 0 → byte 19):
> - String: chega correta ("JIMSMITH...")
> - Inteiros: bytes chegados na ordem big endian são interpretados como little endian → idade vira 21×2²⁴
>
> Inversão de todos os bytes da palavra: corrige os inteiros mas inverte a string para "HTIMS MIJ".
>
> **Não há solução simples** para dados heterogêneos — exige metadado de tipo por campo.

## Contexto Histórico

Termo cunhado por Jonathan Swift em *As Viagens de Gulliver*: grupos guerreavam sobre em qual extremidade quebrar ovos (*big end* ou *little end*). Aplicado à arquitetura de computadores por Cohen (1981).

## Ver também
- [[Memória]] — endereços de memória, células e palavras
- [[Transmissão de Dados]] — network byte order em protocolos TCP/IP
- [[ASCII]] — codificação de 1 byte por caractere; não afetada por endianness
- [[Registrador]] — registradores armazenam palavras conforme endianness da arquitetura
