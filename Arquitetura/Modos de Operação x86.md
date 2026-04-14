---
title: Modos de Operação x86
aliases:
  - modos de operação
  - modo real
  - modo protegido
  - modo protegido de 32 bits
  - modo de 64 bits
  - modo longo
  - modo Virtual-8086
  - modo de compatibilidade
  - SMM
  - System Management Mode
tags:
  - computação/arquitetura
date: 2026-04-09
---

# Modos de Operação x86

## Definição
Processadores [[Intel x86|x86]] podem operar em diferentes modos, cada um com capacidades distintas de endereçamento de memória, tamanho de registradores e recursos disponíveis. O sistema operacional é responsável por instruir o processador a mudar de modo durante a inicialização.

> [!note] Limites de arquitetura vs. hardware
> Os limites de memória discutidos aqui são **limites de arquitetura** (software). O processador pode ter limitações de hardware adicionais (ex: quantidade de pinos de endereço). Na prática, considera-se sempre o menor dos limites — incluindo o número de soquetes da [[Placa-mãe]].

## Relações (SPO)
- Modo Real → emula → processador 8086 (16 bits, 1 MiB)
- Modo Protegido → habilita → proteção de memória e multitarefa
- Modo de 64 bits → expande → registradores para 64 bits e endereçamento
- SMM → responde a → SMI (interrupção de gerenciamento do sistema)
- [[Sistema Operacional]] → instrui → processador a mudar de modo durante o boot

## Visão Geral dos Modos

| Modo | Introduzido | Reg. GPR | Memória máx. (RAM) | Uso típico |
|---|---|---|---|---|
| **Real** | 8086 | 16 bits | 1 MiB | DOS; estado inicial ao ligar |
| **Protegido 16 bits** | 286 | 16 bits | 16 MiB (1 GiB virtual) | Raramente usado |
| **Protegido 32 bits** | 386 | 32 bits | 4 GiB/prog. (64 GiB c/ PAE) | SOs 32 bits (Windows XP etc.) |
| **Virtual-8086** | 386 | 16 bits | 1 MiB por sessão | Janelas DOS no Windows |
| **Compatibilidade** | Athlon 64 / EM64T | 32/16 bits | — | Programas 32 bits em SO 64 bits |
| **64 bits (Longo)** | Athlon 64 / EM64T | 64 bits | 48–57 bits linear; 52 bits físico | SOs 64 bits modernos |
| **SMM** | — | — | — | Gerenciamento térmico |

---

## Modo Real

O processador opera exatamente como um **8086**: registradores de 16 bits, acesso a no máximo **1 MiB de memória**. Foi o modo do sistema operacional **DOS**.

**Estado ao ligar:** todo processador x86 inicia no modo real. O [[Firmware]] (UEFI/BIOS) opera inicialmente neste modo, e o sistema operacional envia uma instrução para mudar ao modo adequado durante o boot.

> [!note] DOS moderno = Virtual-8086, não modo real
> Em sistemas operacionais modernos, uma "janela do DOS" ou programa DOS roda em modo **Virtual-8086**, não no modo real — aproveitando as proteções do modo protegido.

---

## Modo Protegido de 16 bits

Introduzido com o **80286**. Recursos:

- **Proteção de memória:** cada programa em porção separada e protegida de memória (ver [[Memória Virtual]])
- **Multitarefa:** suporte por hardware para alternância rápida entre programas
- **Memória:** até **16 MiB** de RAM; até **1 GiB** com [[Memória Virtual|memória virtual]] (swap file)
- **Segmentação:** memória dividida em segmentos de **64 KiB** — o processador só acessa blocos de 64 KiB por vez

> [!warning] Limitações do 286
> - Não havia instrução para **retornar ao modo real** uma vez no modo protegido
> - A segmentação forçada em blocos de 64 KiB tornava o acesso à memória inconveniente
>
> Por essas limitações, poucos sistemas operacionais usaram esse modo. O 286 geralmente operava no modo real, como um 8086 mais rápido.

---

## Modo Protegido de 32 bits

Introduzido com o **80386**. Base de todos os **sistemas operacionais de 32 bits** (Windows 9x, XP, Linux 32 bits etc.).

### Recursos principais

- **Proteção de memória:** igual ao modo 16 bits, mas com acesso plano (flat) à memória inteira
- **Multitarefa:** suporte por hardware
- **Memória:** até **4 GiB** por programa; com extensões PAE-36 ou PSE-36, o processador pode acessar até **64 GiB** de memória física total
- **Paginação:** mecanismo base da [[Memória Virtual]]; o limite de 4 GiB é RAM + virtual combinados

### Registradores no modo 32 bits

- GPRs expandidos para **32 bits** (EAX, EBX… — exceto registradores de segmento, que permanecem 16 bits)
- **SSE:** 8 registradores XMM de **128 bits** (XMM0–XMM7) para instruções SIMD
- **AVX/FMA/AVX2:** XMM expandidos para **256 bits** → YMM0–YMM7
- **AVX-512:** YMM expandidos para **512 bits** → ZMM0–ZMM7

### Modo Virtual-8086 (V86 / VM86)

Submodo do protegido 32 bits que permite abrir **sessões simulando um processador 8086** dentro do ambiente protegido.

Vantagens sobre o modo real:
- Não é necessário sair do sistema operacional para rodar programas DOS
- Cada sessão V86 é **protegida em memória** — um programa DOS travado não afeta o SO
- Suporte a **multitarefa**: várias janelas DOS simultâneas

---

## Modo de Compatibilidade

Disponível em processadores com suporte a **64 bits** (chamadas **Intel 64 / IA-32e / EM64T** pela Intel e **x86-64** pela AMD) quando rodando um sistema operacional de 64 bits.

Permite executar programas de **16 ou 32 bits** em um SO 64 bits **sem recompilação**, alternando para modo protegido 16 ou 32 bits conforme necessário.

---

## Modo de 64 bits (Modo Longo)

Requer sistema operacional de 64 bits e programas de 64 bits. Chamado **modo longo** pela AMD.

### Adições ao modo protegido 32 bits

- **GPRs:** expandidos de 32 para **64 bits** (RAX, RBX…)
- **Novos GPRs:** **R8–R15** (8 registradores de uso geral de 64 bits)
- **Novos registradores de controle:** CR8–CR15 (64 bits)
- **Novos registradores SIMD:** XMM8–XMM15 (128 bits); com AVX: YMM0–YMM15 (256 bits); com AVX-512: ZMM0–ZMM31 (512 bits, incluindo 16 novos)

### Endereçamento

| Tipo | Bits | Capacidade |
|---|---|---|
| Linear (por programa) — paginação 4 níveis | 48 bits | 256 TiB |
| Linear (por programa) — paginação 5 níveis | 57 bits | 128 PiB |
| Físico (total) | até 52 bits | até 4 PiB |

> [!note] Registradores de 64 bits ≠ endereçamento de 64 bits
> Apesar dos registradores serem de 64 bits, o endereçamento linear é de 48 ou 57 bits e o físico de até 52 bits. Para calcular a memória máxima correspondente: 2^N bytes. Exemplo: 2^40 = 1 TiB.
>
> A instrução `CPUID` permite descobrir os bits de endereçamento suportados pelo processador.

> [!warning] Multitarefa no modo de 64 bits
> O suporte ao controle de multitarefa por **hardware** foi **removido** no modo de 64 bits. A multitarefa é controlada **exclusivamente pelo sistema operacional**.

### Principais vantagens do modo de 64 bits
1. Acesso a muito mais memória física e virtual
2. Mais registradores GPR e SIMD disponíveis
3. Compatibilidade mantida via modo de compatibilidade para programas 32 bits

---

## Modo de Gerenciamento do Sistema (SMM)

O **SMM** (System Management Mode) é ativado por uma **SMI** (System Management Interrupt — Interrupção de Gerenciamento do Sistema), normalmente gerada pela [[Placa-mãe]] ([[Chipset]]) em situações de **gerenciamento térmico** ou outras necessidades de manutenção de baixo nível.

Ao receber a SMI, o processador:
1. Salva o conteúdo de todos os seus [[Registrador|registradores]]
2. Entra no SMM e executa a rotina de gerenciamento
3. Restaura o conteúdo dos registradores
4. Retoma a tarefa (programa/SO) interrompida

O SMM é transparente ao sistema operacional — ele não percebe que a interrupção ocorreu.

---

## Ver também
- [[Proteção de Memória x86]] — GDT/LDT/IDT, descritores, rings de privilégio, GPF
- [[Intel x86]] — evolução histórica dos processadores x86
- [[Processador]] — arquitetura interna, pipeline, registradores
- [[Registrador]] — GPRs, flags, stack pointer
- [[Memória Virtual]] — paginação e espaço de endereçamento virtual
- [[Firmware]] — boot começa no modo real
- [[Interrupção]] — SMI como tipo especial de interrupção de hardware
- [[Chipset]] — gera a SMI para o SMM
