---
title: História do UNIX e do Linux
aliases:
  - historia do UNIX
  - história do Linux
  - UNIX portátil
  - Berkeley UNIX
  - MINIX
tags:
  - computação/sistemas
source: "Sistemas Operacionais Modernos, cap. 10"
created_by: Codex
---

# História do UNIX e do Linux

## Definição

Linha histórica de sistemas compatíveis com UNIX, indo do UNICS original ao Linux, com ênfase em portabilidade, padronização e influência acadêmica.

## Papel técnico

UNIX consolidou a ideia de sistema operacional pequeno, escrito majoritariamente em C, com chamadas de sistema simples, arquivos como abstração uniforme e ferramentas combináveis pelo [[Shell]]. Linux herda esse modelo mas troca a linhagem proprietária por desenvolvimento aberto, núcleo monolítico modular e suporte amplo de hardware.

## Relações (SPO)

- UNICS -> evolui para -> UNIX
- UNIX portátil -> depende de -> linguagem C
- Berkeley UNIX -> adiciona -> rede, sockets e ferramentas de sistema
- POSIX -> padroniza -> interface UNIX
- MINIX -> influencia -> criação do Linux
- Linux -> implementa -> interface semelhante a UNIX

## Linha do Tempo

### Pré-UNIX: compartilhamento de tempo

- 1940-50: computadores "pessoais" — um usuário por vez, por reserva
- 1960s: sistemas em lote (cartões perfurados → horas de espera)
- Compartilhamento de tempo como solução:
  - **Dartmouth**: só BASIC; sucesso curto
  - **MIT (CTSS)**: propósito geral; sucesso acadêmico
  - **MULTICS**: MIT + Bell Labs + GE; sistema de segunda geração excessivamente ambicioso

### UNICS (1969)

Bell Labs saiu do MULTICS. **Ken Thompson** escreveu um MULTICS enxuto em assembly para um PDP-7 descartado. **Brian Kernighan** chamou ironicamente de UNICS (*UNiplexed Information and Computing Service*). Nome mudou para UNIX.

### PDP-11 UNIX

**Dennis Ritchie** juntou-se a Thompson. UNIX migrou para PDP-11/20, depois 11/45 e 11/70.

Dois desenvolvimentos críticos:
1. Hardware com proteção de memória → múltiplos usuários
2. Reescrita em **C** (Ritchie criou C a partir de B → BCPL) → portabilidade

UNIX V6 (1975): licenciada a universidades por taxa modesta. PDP-11 dominava departamentos acadêmicos; UNIX preencheu o vácuo com código-fonte completo.

### Berkeley UNIX (BSD)

Berkeley adicionou extensões ao V6:
- **3BSD** (1979): memória virtual
- **4.1BSD** (1981): novo escalonador
- **4.2BSD** (1983): TCP/IP integrado → base da internet atual
- **4.4BSD**: base do FreeBSD, macOS

### UNIX Padrão

AT&T lançou System III (1982) e System V (1983). Proliferação de variantes incompatíveis gerou pressão por padronização.

**POSIX** (IEEE 1003.1): define conjunto de rotinas (`open`, `read`, `fork`) que todo UNIX compatível deve fornecer. Portabilidade de software entre variantes UNIX.

### MINIX (1987)

**Andrew Tanenbaum** criou MINIX para fins educativos: micronúcleo com ~1.600 linhas C + 800 assembly. Gerenciamento de memória e FS empurrados para processos de usuário. Base de estudos de Linus Torvalds.

### Linux (1991–)

**Linus Torvalds** criou clone do UNIX para produção, inspirado no MINIX mas **monolítico**.

| Versão | Ano | Tamanho | Destaques |
|--------|-----|---------|-----------|
| 0.01 | 1991 | ~9.300 C + 950 asm | Primeiro lançamento; desenvolvido em máquina MINIX |
| 1.0 | 1994 | ~165k linhas | VM, FS avançado, TCP/IP, rede BSD |
| 2.0 | 1996 | ~470k C + 8k asm | 64 bits, SMP, múltiplos protocolos |
| 3.0 | 2011 | — | Aniversário de 20 anos |
| atual | 2013+ | ~16M linhas | Toda arquitetura moderna |

**Licença GPL** (Stallman / FSF): código-fonte deve acompanhar o produto ou estar disponível sob solicitação.

## Árvore Genealógica

```
MULTICS → UNICS/UNIX (Bell Labs, 1969)
        → BSD (Berkeley) → FreeBSD, macOS
        → System V (AT&T) → Solaris, AIX, HP-UX
MINIX → Linux (1991)
```

## Ver também

- [[Shell]]
- [[Sistema Operacional]]
- [[Visão Geral do Linux]]
- [[Processos no Linux]]
- [[Interface de Programa Linux x86]]
- [[Descritores de Arquivo UNIX]]
