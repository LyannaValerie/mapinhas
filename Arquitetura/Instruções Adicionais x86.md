---
title: Instruções Adicionais x86
tags:
  - hardware
  - arquitetura-x86
  - segurança
  - instruções
aliases:
  - SMX
  - TXT
  - SGX
  - SME
  - SEV
  - TME
  - TSX
  - MPX
  - BMI
  - RDRAND
  - Key Locker
  - extensões x86
---

# Instruções Adicionais x86

Extensões opcionais à arquitetura x86. Disponibilidade verificável via `CPUID` (HWiNFO64 ou CPU Database). Ver também [[Instruções SIMD]] (cap. 14) e instruções VMX/SVM (cap. 16 → [[Máquina Virtual]]).

---

## SMX + TXT (Segurança de Execução)

| Item | Detalhe |
|------|---------|
| **TXT** (Trusted eXecution Technology) | Codinome *LaGrande*; exclusivo Intel |
| Objetivo | Proteger dados contra roubo; criptografia em armazenamento e transferência entre componentes |
| Requisito | Módulo **TPM** (Trusted Platform Module) na placa-mãe |
| **SMX** (Safer Mode eXtensions) | Conjunto de instruções que implementa o TXT |
| Lançamento | Microarquitetura Core (2006); voltado a servidores |

---

## Criptografia de Memória

### Intel SGX (Software Guard eXtensions) — Skylake (2015)

- Cria **enclaves**: regiões privadas na RAM, criptografadas e protegidas
- Nem SO nem hypervisor conseguem acessar um enclave
- **Não** criptografa memória inteira nem VMs — apenas regiões definidas pelo programa

### AMD — Zen+ (mercado corporativo/servidores: Ryzen Pro, Threadripper Pro, EPYC)

Processador de criptografia integrado com três modos:

| Tecnologia | Escopo | Requisito |
|-----------|--------|-----------|
| **SME** (Secure Memory Encryption) | Páginas de memória selecionadas | Suporte do SO |
| **SEV** (Secure Encrypted Virtualization) | Máquinas virtuais inteiras | Suporte do SO |
| **TSME** / Memory Guard | Memória inteira (transparente) | Ativável via BIOS/UEFI mesmo sem suporte do SO |

Evolução do SEV:

| Versão | Introduzida em | Adiciona |
|--------|---------------|---------|
| SEV | Zen | Criptografia da VM |
| **SEV-ES** | Zen 2 | Criptografia do **estado dos registradores** — hypervisor não acessa |
| **SEV-SNP** | Zen 3 | Paginação aninhada segura — VM só lê dados que ela mesma gravou (anti-replay/tampering) |

### Intel TME / MKTME — Sunny Cove + Tremont (2020)

| Tecnologia | Escopo | Chaves |
|-----------|--------|--------|
| **TME** (Total Memory Encryption) | Memória inteira | 1 chave |
| **MKTME** (Multi-Key TME) | Memória inteira | Múltiplas chaves |

---

## TSX (Transactional Synchronization Extensions) — Intel Haswell (2013)

Problema: travas (locks) de software em regiões de memória compartilhada entre threads reduzem desempenho — criadas sempre, mesmo quando desnecessárias.

Solução TSX: **memória transacional por hardware**
- Remove as travas; executa operações imediatamente
- Processador monitora conflitos de acesso em tempo real
- Conflito detectado → aborta transação e restaura valor anterior

> [!note] Exclusivo Intel — não suportado pela AMD

---

## MPX (Memory Protection eXtensions) — Intel Skylake (2015) → descontinuado 2019

- Detecta bugs de **buffer overflow / underflow** em tempo de execução
- Adiciona 4 registradores de limite: **BND0–BND3** (128 bits cada = 64 bits limite inferior + 64 bits limite superior)
- Registradores auxiliares: **BNDCFGU**, **BNDCFGS** (config, 64 bits) e **BNDSTATUS** (estado, 64 bits)
- 8 novas instruções + esquema de tradução de endereços em 2 níveis
- Intel removeu suporte em 2019 — processadores atuais não suportam

---

## Manipulação de Bits

| Extensão | Fabricante | Disponibilidade | Notas |
|---------|-----------|----------------|-------|
| **BMI1** | Intel | Haswell+ | AMD: Jaguar+ |
| **BMI2** | Intel | Haswell+ | AMD: Excavator+ |
| **ABM** (Advanced Bit Manipulation) | AMD | — | `POPCNT` (absorvido pelo SSE4.2) + `LZCNT` (absorvido pelo BMI1 Intel) |
| **TBM** (Trailing Bit Manipulation) | AMD | Piledriver | Removido no Jaguar e Zen; Intel nunca suportou |

---

## ADX (Multi-Precision Add-Carry) — Intel Broadwell / AMD Zen+

Melhora desempenho em aritmética de inteiros maiores que os registradores de uso geral (ex: criptografia com números de 256+ bits).

---

## CLMUL (Carry-Less Multiplication)

Multiplicação de polinômios em campo finito / multiplicação de 64 bits sem carry.  
Uso típico: cálculo de **CRC** e criptografia.

| Fabricante | Disponibilidade |
|-----------|----------------|
| Intel | Westmere+ |
| AMD | Bulldozer+ |

---

## RDRAND — Gerador de Números Aleatórios por Hardware

Problema: RNG tradicional usa relógio do sistema → **pseudoaleatório** (atacante com a hora pode reproduzir o número).

Solução: RNG digital integrado no processador → números **verdadeiramente aleatórios**.

| Fabricante | Disponibilidade | Nome comercial |
|-----------|----------------|----------------|
| Intel | Ivy Bridge+ | Intel Secure Key Technology |
| AMD | Excavator, Puma, Zen+ | — |

---

## RDSEED — Intel Broadwell+ / AMD Zen+

Usa o RNG do processador (baseado em **ruído térmico da pastilha**) como semente para geradores pseudoaleatórios de software — em vez do relógio do sistema.

---

## Key Locker — Intel Willow Cove (2020)

Criptografia/descriptografia sem expor chaves originais na RAM:

- Gera **chaves temporárias** (wrapping handles) equivalentes às originais
- Chaves temporárias: válidas só na máquina onde foram criadas
- Revogadas por software ou por **reinicialização**
- Chaves originais nunca ficam na RAM durante operação normal → imunes a ataques de dump de memória
- Chaves temporárias roubadas = inúteis em outra máquina

---

## Ver também

- [[Instruções SIMD]] — outras extensões x86 (MMX/SSE/AVX/AVX-512/AMX)
- [[Máquina Virtual]] — SGX/SEV/TXT se relacionam com isolamento de VMs e enclaves
- [[Paginação x86]] — SEV-SNP usa paginação aninhada segura
- [[Gerenciamento de Consumo Elétrico]] — não relacionado, mas também usa CPUID para detecção
- [[Registradores x86]] — BND0–BND3 (MPX), registradores de estado (BNDSTATUS)
