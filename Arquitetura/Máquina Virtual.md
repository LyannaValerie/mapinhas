---
title: Máquina Virtual
aliases:
  - virtual machine
  - VM
  - virtualização
  - hypervisor
  - VMware
  - VirtualBox
  - VT-x
  - AMD-V
  - SVM
  - VMX
  - KVM
  - Hyper-V
tags:
  - computação/arquitetura
  - hardware
  - arquitetura-x86
date: 2026-04-07
---

# Máquina Virtual

## Definição
Abstração do **computador inteiro** — incluindo o [[Sistema Operacional]], o [[Processador]] e os programas em execução. Permite que um único computador físico execute múltiplos [[Sistema Operacional|sistemas operacionais]] simultaneamente, cada qual acreditando ter uso exclusivo do hardware.

## Relações (SPO)
- Máquina Virtual → abstrai → computador inteiro (SO + processador + programas)
- Máquina Virtual → introduzida por → IBM (anos 1960)
- Máquina Virtual → permite → múltiplos SOs em um único hardware
- Máquina Virtual → diferente de → "máquina virtual" no sentido de [[Níveis de Abstração]] (Tanenbaum)

---

## Origem histórica

A ideia de máquina virtual foi introduzida pela **IBM nos anos 1960** como forma de permitir que diferentes programas e sistemas operacionais compartilhassem o mesmo hardware de mainframe. Cada usuário enxergava sua própria "máquina virtual" isolada.

---

## Uso moderno

Tornou-se proeminente como forma de gerenciar computadores que precisam executar **programas projetados para múltiplos sistemas operacionais**:

| Caso de uso | Exemplo |
|---|---|
| Múltiplos SOs num único computador | Executar Windows, macOS e Linux no mesmo hardware |
| Isolamento de aplicações | Cada VM tem seu próprio SO e ambiente |
| Servidores em nuvem | Um servidor físico hospeda dezenas de VMs independentes |
| Compatibilidade | Rodar software legado de um SO em outro |

---

## Posição na hierarquia de abstrações (CS:APP)

O CS:APP (Bryant & O'Hallaron) organiza as abstrações de um sistema computacional em duas camadas:

```
┌─────────────────────────────────────────────────────┐
│               Máquina Virtual                       │  ← abstração do computador inteiro
├─────────────────────────────────────────────────────┤
│      Processos      │  Memória Virtual  │  Arquivos │  ← abstrações do SO
├─────────────────────┴───────────────────┴───────────┤
│         Instruction Set Architecture (ISA)          │  ← abstração do processador
├─────────────────────────────────────────────────────┤
│   Processador  │  Memória Principal  │  Disp. E/S   │  ← hardware real
└─────────────────────────────────────────────────────┘
```

- **ISA** abstrai o processador físico → programas veem um modelo sequencial simples, mesmo que o hardware execute em paralelo
- **[[Processo]]**, **[[Memória Virtual]]** e **[[Arquivo]]** são as três abstrações do SO sobre o hardware
- **Máquina Virtual** abstrai o nível completo acima (SO + hardware) → permite múltiplos SOs

---

## Distinção: VM de Tanenbaum vs. VM moderna

> [!warning] Dois significados de "máquina virtual"
> - **Tanenbaum ([[Níveis de Abstração]]):** "máquina virtual" = cada nível da hierarquia multinível (ex: a "máquina" que entende Python). Conceito abstrato de camada.
> - **CS:APP / uso moderno:** "máquina virtual" = software que emula um computador físico completo, permitindo rodar um SO inteiro sobre outro (VMware, VirtualBox, KVM, Hyper-V).
>
> O segundo é uma instância concreta e implementável do primeiro — mas o termo é usado de formas diferentes nas duas tradições.

---

---

## Virtualização x86 — Hardware

### Distinção de tecnologias vizinhas

| Tecnologia | O que é | Múltiplos SOs? |
|-----------|---------|---------------|
| **Multitarefa** | 1 SO, N programas alternando | Não |
| **Multi-core / HT** | N núcleos/threads, 1 SO | Não |
| **Virtualização** | N SOs em paralelo, cada um isolado | **Sim** |

> [!note] Modo Virtual 8086 (V86) como precursor
> V86 (cap. 5) cria máquinas virtuais 8086 para rodar DOS em paralelo. Virtualização x86 generaliza isso para SOs completos modernos.

---

### Suporte por hardware

- **Intel VT / VT-x** (codinome *Vanderpool*): desde 2005
- **AMD-V / SVM** (codinome *Pacifica*): desde 2006
- Detectável via instrução `CPUID` (ou HWiNFO64)

Virtualização I/O:
- **Intel VT-d** — virtualização de dispositivos de E/S
- **AMD-Vi / IOMMU** (Input/Output Memory Management Unit) — equivalente AMD

Intel também tem **VT-c** (virtualização de rede) e **VT-i** (para Itanium, descontinuado em 2021).

---

### Hypervisor (VMM — Virtual Machine Monitor)

Controla as máquinas virtuais. Dois tipos:

| Tipo | Nome | Roda sobre | Uso típico | Exemplos |
|------|------|-----------|------------|---------|
| **Tipo 1** | Bare metal | Diretamente no hardware | Data centers | VMware vSphere/ESXi, Hyper-V, KVM, Xen, Oracle VM, Citrix Hypervisor |
| **Tipo 2** | Hosted | Sobre um SO existente | Profissionais / usuários finais | VirtualBox, VMware Workstation Pro, VMware Fusion |

- **Convidado (guest):** SO rodando dentro de uma VM
- Cada VM é completamente isolada — vírus numa VM não afeta outras

---

### Modos de execução na virtualização

| Modo | Quem usa |
|------|---------|
| **Raiz (root)** | Hypervisor |
| **Não-raiz (non-root)** | SOs convidados |

---

### Intel VT-x — Instruções VMX (13 instruções)

| Instrução | Função |
|-----------|--------|
| `VMXON` | Entra no modo de virtualização |
| `VMXOFF` | Sai do modo de virtualização |
| `VMLAUNCH` | Cria/inicia máquina virtual |
| `VMRESUME` | Retorna a uma VM (restaura estado) |
| `VMCALL` | Sai da VM → hypervisor (salva estado) |
| `VMREAD` / `VMWRITE` | Lê/escreve estrutura de controle da VM |
| `VMPTRLD` / `VMPTRST` | Carrega/salva ponteiro para estrutura da VM |
| `VMCLEAR` | Limpa estrutura da VM |
| `INVEPT` / `INVVPID` | Invalida entradas de cache de paginação estendida |
| `VMFUNC` | Chama funções do hypervisor sem saída completa da VM |

Fluxo: `VMXON` → `VMLAUNCH` (convidado) → `VMCALL` (sai ao hypervisor) → `VMRESUME` (volta ao convidado) → `VMXOFF`

---

### AMD-V — Instruções SVM (7 instruções)

| Instrução | Função |
|-----------|--------|
| `VMRUN` | Cria/inicia máquina virtual |
| `VMLOAD` | Retorna a uma VM (restaura estado) |
| `VMMCALL` / `VMSAVE` | Sai da VM → hypervisor (salva estado) |
| `CLGI` / `STGI` | Controla interrupções globais |
| `SKINIT` | Inicialização segura |

> [!note] Diferença chave AMD-V vs VT-x
> AMD-V não tem instrução para entrar no modo de virtualização. Usa o bit **SVME (bit 12)** do registrador **EFER** (MSR). Para desativar: zerar o mesmo bit.

---

### Virtualização de Segunda Geração

Problema: sem extensão, o hypervisor precisa **sair da VM** para traduzir endereços de memória do convidado → overhead.

Solução: cada convidado tem sua própria tabela de páginas de nível extra.

| Extensão | Fabricante | Nome das tabelas |
|---------|-----------|-----------------|
| **EPT** (Extended Page Tables) | Intel | EPT |
| **RVI** (Rapid Virtualization Index) | AMD | **NPT** (Nested Paging Tables) |

Resultado: tradução de endereços feita no hardware sem sair da VM → desempenho significativamente maior.

---

## Ver também
- [[Paginação x86]] — EPT/NPT são extensões das tabelas de página normais
- [[Modos de Operação x86]] — modo V86 como precursor da virtualização
- [[Multitarefa]] — diferença entre multitarefa e virtualização
- [[Desempenho do Processador]] — HT/multi-core (≠ virtualização)
- [[Registradores x86]] — EFER (MSR) contém flag SVME da AMD-V
- [[Sistema Operacional]] — uma VM executa um SO completo
- [[Processo]] — abstração do SO abaixo da VM
- [[Memória Virtual]] — abstração do SO abaixo da VM
- [[ISA]] — abstração do processador; a VM apresenta uma ISA ao SO hóspede
- [[Níveis de Abstração]] — sentido original de "máquina virtual" (Tanenbaum)
- [[Computação em Nuvem]] — servidores em nuvem são amplamente baseados em VMs
