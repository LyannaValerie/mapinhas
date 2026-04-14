---
title: Registradores x86
aliases:
  - GPR x86
  - registradores de uso geral x86
  - EAX
  - RAX
  - AX
  - AH
  - AL
  - EFLAGS
  - RFLAGS
  - CS
  - SS
  - DS
  - ES
  - FS
  - GS
  - CR0
  - CR3
  - GDTR
  - IDTR
  - segment registers
tags:
  - computação/arquitetura
date: 2026-04-09
---

# Registradores x86

## Definição
Catálogo dos registradores da arquitetura [[Intel x86]]. Divididos em grupos funcionais, cada um acessível em diferentes tamanhos dependendo do [[Modos de Operação x86|modo de operação]] do processador.

---

## Registradores de Uso Geral (GPRs)

A arquitetura x86 possui **16 GPRs**. Os quatro clássicos (A, B, C, D) existem desde o 8086; os R8–R15 foram adicionados no modo de 64 bits.

### Registradores clássicos (A, B, C, D)

| Propósito | 64 bits | 32 bits | 16 bits | 8 bits alto | 8 bits baixo |
|---|---|---|---|---|---|
| Acumulador | RAX | EAX | AX | AH | AL |
| Base | RBX | EBX | BX | BH | BL |
| Contador | RCX | ECX | CX | CH | CL |
| Dados | RDX | EDX | DX | DH | DL |

> [!note] Uso convencional
> - **A (Acumulador):** armazena dados a manipular e resultados de operações
> - **B (Base):** informações de endereçamento
> - **C (Contador):** contador em laços (loops)
> - **D (Dados):** E/S; parte do resultado de multiplicação/divisão (a outra parte fica em A)
>
> Algumas instruções x86 exigem um registrador específico — por isso o "uso geral" tem limites.

**Acesso parcial:** AL/AH são os 8 bits baixo/alto de AX, que é a metade baixa de EAX, que é a metade baixa de RAX. Em todos os modos é possível acessar qualquer nível.

### Registradores R8–R15 (modo de 64 bits apenas)

| 64 bits | 32 bits | 16 bits | 8 bits |
|---|---|---|---|
| R8 | R8D | R8W | R8B |
| … | … | … | … |
| R15 | R15D | R15W | R15B |

Sufixos: **D** = Double Word (32 bits), **W** = Word (16 bits), **B** = Byte (8 bits).

> [!warning] R8–R15 disponíveis apenas no modo de 64 bits

---

## Registradores de Índice

Usados para endereçar origem e destino em instruções de manipulação de strings (MOVS, CMPS, SCAS, LODS, STOS).

| Nome completo | 64 bits | 32 bits | 16 bits | 8 bits* |
|---|---|---|---|---|
| Source Index (índice de origem) | RSI | ESI | SI | SIL |
| Destination Index (índice de destino) | RDI | EDI | DI | DIL |

*8 bits disponíveis apenas no modo de 64 bits.

---

## Registradores de Ponteiro

| Nome completo | 64 bits | 32 bits | 16 bits | 8 bits* |
|---|---|---|---|---|
| Base Pointer (ponteiro base) | RBP | EBP | BP | BPL |
| Stack Pointer (ponteiro da pilha) | RSP | ESP | SP | SPL |
| Instruction Pointer (ponteiro de instrução) | RIP | EIP | IP | — |

*8 bits disponíveis apenas no modo de 64 bits (exceto RIP).

- **RBP/EBP/BP:** endereço base da [[Pilha]], usado para referenciar parâmetros e variáveis locais no frame da função
- **RSP/ESP/SP:** endereço do topo da [[Pilha]] — ver [[Pilha#Stack Pointer (SP)]]
- **RIP/EIP/IP:** endereço da próxima instrução a ser buscada — o **Program Counter** em x86. Junto com **CS**, forma o contador de programa completo

---

## Registradores de Segmento

Sempre de **16 bits**, mesmo no modo protegido de 32 bits ou no modo de 64 bits. Usados para segmentação (modo real) ou como seletores de segmento (modo protegido).

| Registrador | Nome completo | Uso clássico (modo real) |
|---|---|---|
| **CS** | Code Segment | Com IP/EIP/RIP forma o contador de programa (PC) |
| **SS** | Stack Segment | Indica o segmento onde a [[Pilha]] está localizada |
| **DS** | Data Segment | Segmento padrão para acesso a dados; usado quando nenhum outro é especificado |
| **ES** | Extra Segment | Acesso a dados; offset dado pelo índice de destino (DI) ou pela instrução |
| **FS** | F Segment | Acesso a dados; offset dado pela instrução (a partir do 386) |
| **GS** | G Segment | Idem FS (a partir do 386) |

Ver [[Modos de Operação x86]] para o papel de segmentação em cada modo.

---

## Registrador de Flags — EFLAGS / RFLAGS

Um único registrador agrupa todas as flags do processador:

| Modo | Registrador | Bits |
|---|---|---|
| Modo real (8086/286) | FLAGS | 16 bits |
| Modo protegido 32 bits | EFLAGS | 32 bits |
| Modo de 64 bits | RFLAGS | 64 bits (bits 63–32 reservados = 0) |

### Flags de status (resultado de operações)

| Bit | Símbolo | Nome | Ativada quando |
|---|---|---|---|
| 0 | CF | Carry Flag | "Vai um" ou empréstimo no último cálculo |
| 2 | PF | Parity Flag | Número de uns no byte menos significativo é par |
| 4 | AF | Auxiliary Carry Flag | "Vai um" do nibble baixo para o alto (BCD) |
| 6 | ZF | Zero Flag | Resultado é zero |
| 7 | SF | Sign Flag | Bit mais significativo = 1 (resultado negativo) |
| 11 | OF | Overflow Flag | Resultado excede a capacidade do destino |

### Flag de controle

| Bit | Símbolo | Nome | Função |
|---|---|---|---|
| 10 | DF | Direction Flag | Controla direção das instruções de string: 0 = crescente, 1 = decrescente |

### Flags de sistema

| Bit | Símbolo | Nome | Função |
|---|---|---|---|
| 8 | TF | Trap Flag | Modo single-step para debug |
| 9 | IF | Interrupt Enable Flag | Habilita/desabilita interrupções mascaráveis |
| 12–13 | IOPL | I/O Privilege Level | Nível de privilégio para E/S |
| 14 | NT | Nested Task | Indica tarefa encadeada |
| 16 | RF | Resume Flag | Suprime exceções de debug após retomada |
| 17 | VM | Virtual-8086 Mode | Ativa modo Virtual-8086 |
| 18 | AC | Alignment Check | Verifica alinhamento de acessos à memória |
| 19 | VIF | Virtual Interrupt Flag | Cópia virtual de IF |
| 20 | VIP | Virtual Interrupt Pending | Interrupção virtual pendente |
| 21 | ID | ID Flag | Processador suporta instrução CPUID se pode ser modificado |

Ver [[Flag]] para contexto geral; ver [[Modos de Operação x86]] para o papel de VM e IOPL.

---

## Registradores de Controle (CR)

Controlam o modo de operação e características da tarefa em execução.

| Registrador | Bits (32/64) | Função |
|---|---|---|
| **CR0** | 32 / 64 | Flags de modo e estado do processador (PG, CD, NW, AM, WP, NE, ET, TS, EM, MP, **PE**) |
| **CR2** | 32 / 64 | Endereço linear da instrução que causou a última **falha de página** |
| **CR3** | 32 / 64 | Endereço físico da primeira tabela da estrutura de **paginação** |
| **CR4** | 32 / 64 | Flags de features adicionais (PAE, VME, PSE, PCE, OSFXSR, OSXMMEXCPT…) |
| **CR8** | 64 apenas | Task Priority Register (TPR) — acesso ao registrador de prioridade |
| **XCR0** | 64 | Estados estendidos para XSAVE/XRSTOR (SSE, AVX, ZMM…) — requer OSXSAVE em CR4 |

> [!note] CR0 — bit PE
> O bit **PE** (Protection Enable) do CR0 é o que habilita o modo protegido. O sistema operacional o ativa durante o boot para sair do modo real.

> [!note] CR2 e CR3 — paginação
> CR2 e CR3 são a interface entre o hardware de paginação e o SO. CR3 aponta para a tabela de páginas ativa; CR2 indica onde ocorreu uma page fault. Ver [[Memória Virtual]].

---

## Registradores de Debug (DR)

Suportam o processo de depuração de programas.

| Registrador | Função |
|---|---|
| DR0–DR3 | Endereços de parada (breakpoints) |
| DR6 | Registrador de estado — indica condições da parada |
| DR7 | Registrador de controle — define tipos de acesso que geram parada |

No modo protegido 32 bits: 32 bits cada. No modo de 64 bits: expandidos para 64 bits. Até **4 breakpoints** simultâneos (DR0–DR3).

---

## Registradores de Gerenciamento de Memória

Usados pelo sistema de segmentação e gerenciamento de tarefas.

| Registrador | Bits | Função |
|---|---|---|
| **GDTR** | 48 (32 bits) / 80 (64 bits) | Endereço base + limite da Global Descriptor Table |
| **LDTR** | 16 | Seletor de segmento da Local Descriptor Table |
| **IDTR** | 48 (32 bits) / 80 (64 bits) | Endereço base + limite da tabela de [[Interrupção|interrupções]] (IDT) |
| **TR** | 16 | Seletor do TSS (Task State Segment) para controle de tarefas |

> [!note] Estrutura dos registradores de tabela (GDTR/IDTR)
> Os 16 bits menos significativos armazenam o **limite** (tamanho máximo da tabela em bytes); os bits mais significativos armazenam o **endereço linear** (32 ou 64 bits conforme o modo).

---

## Registradores de Modelos Específicos (MSR)

Registradores que podem não existir em todos os modelos e podem ser removidos em modelos futuros.

| MSR | Função |
|---|---|
| **MTRR** (Memory Type Range Register) | Configuração da memória para o circuito de cache |
| **EFER** (Extended Feature Enable Register) | Controla recursos adicionais do processador |

---

## Registradores da Unidade de Ponto Flutuante

Os registradores FPU/SSE/AVX são estudados em conjunto com a unidade de ponto flutuante. Ver [[FPU]] para a hierarquia completa.

| Família | Registradores | Tamanho |
|---|---|---|
| x87 | R0–R7 / ST(0)–ST(7) | 80 bits (pilha) |
| MMX | MM0–MM7 | 64 bits |
| SSE | XMM0–XMM15 | 128 bits |
| AVX | YMM0–YMM15 | 256 bits |
| AVX-512 | ZMM0–ZMM31 | 512 bits |

Os registradores SIMD (XMM, YMM, ZMM) disponíveis por modo estão listados em [[Modos de Operação x86]]. **XCR0** (em [[#Registradores de Controle (CR)]]) controla quais estados SIMD são salvos/restaurados pelo SO via XSAVE/XRSTOR.

---

## Ver também
- [[Registrador]] — conceito geral de registrador
- [[FPU]] — registradores x87, MMX/SSE/AVX/AVX-512 e histórico de coprocessadores
- [[Modos de Operação x86]] — tamanho e disponibilidade dos registradores por modo
- [[Pilha]] — RSP/RBP como ponteiros de pilha
- [[Flag]] — contexto geral das flags
- [[Memória Virtual]] — CR2/CR3 no mecanismo de paginação
- [[Interrupção]] — IDTR aponta para a tabela de tratamento de interrupções
- [[Intel x86]] — evolução histórica dos registradores
