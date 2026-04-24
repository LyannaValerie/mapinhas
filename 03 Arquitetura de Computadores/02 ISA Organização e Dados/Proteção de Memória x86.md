---
title: Proteção de Memória x86
aliases:
  - proteção de memória
  - rings de privilégio
  - ring 0
  - ring 3
  - DPL
  - RPL
  - GDT
  - LDT
  - IDT
  - Global Descriptor Table
  - Local Descriptor Table
  - Interrupt Descriptor Table
  - descritor de segmento
  - seletor de segmento
  - segment selector
  - segment descriptor
  - General Protection Fault
  - GPF
tags:
  - computação/arquitetura
date: 2026-04-09
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Proteção de Memória x86

## Definição
Mecanismo do modo protegido e do modo de 64 bits da arquitetura [[Intel x86]] que divide a memória em **segmentos controlados**, impedindo que um programa acesse ou destrua a área de memória de outro programa.

---

## Por que proteção é necessária

> [!example] Sem proteção: Windows 3.x e 95/98/ME
> O Windows 3.x usava o modo protegido mas **não configurava segmentos individuais por programa** — toda a memória ficava em um único grande segmento compartilhado. Resultado: qualquer programa podia invadir a área de outro, gerando o famoso **GPF (General Protection Fault — Falha Geral de Proteção)**.
>
> O Windows 95, 98 e ME não protegiam a área usada por programas Windows de 16 bits pelo mesmo motivo. Isso tornava esses sistemas significativamente menos estáveis que os sistemas modernos.

Sistemas operacionais modernos usam proteção de segmento para **todos os programas**, tornando falhas de um programa incapazes de corromper outros ou o próprio SO.

---

## Informações por segmento

Para cada segmento de memória, o sistema operacional precisa registrar:

| Informação | Descrição |
|---|---|
| **Tarefa associada** | Qual programa "dono" do segmento |
| **Endereço inicial** | Onde o segmento começa na memória |
| **Tamanho** | Comprimento do segmento |
| **Tipo de acesso** | Somente leitura / somente execução / leitura+gravação |
| **Nível de privilégio** | Nível mínimo para acessar o segmento |
| **Tipo de conteúdo** | Código (instruções), dados, ou tipo especial do SO |
| **Localização** | Na RAM ou no arquivo de troca (swap) |

---

## Níveis de Privilégio (Rings)

Processadores x86 dividem programas em **4 níveis de privilégio** (rings), numerados de 0 (maior privilégio) a 3 (menor):

| Ring | Nível | Quem usa |
|---|---|---|
| **0** | Maior privilégio | Núcleo (**kernel**) do sistema operacional |
| **1** | — | Serviços do SO (drivers de dispositivos) |
| **2** | — | Serviços do SO (drivers de dispositivos) |
| **3** | Menor privilégio | Aplicações do usuário |

> [!note] Rings 1 e 2 em desuso
> Na prática, a maioria dos sistemas operacionais modernos (Linux, Windows) usa apenas os rings **0 e 3** — kernel em ring 0, tudo mais em ring 3. Os rings 1 e 2 existem na arquitetura mas raramente são usados.

---

## Tabelas de Descrição

As informações de cada segmento (chamadas **descritores de segmento**) são armazenadas na [[Memória|RAM]] em estruturas chamadas **tabelas de descrição**.

| Característica | Valor |
|---|---|
| Tamanho por entrada | 8 bytes (64 bits) no modo protegido |
| Tamanho por entrada | 16 bytes (128 bits) no modo de 64 bits |
| Entradas por tabela | Até **8.192** (2¹³) |
| Tamanho máximo da tabela | 64 KiB (8.192 × 8 bytes) |

### Tipos de tabela

| Tabela | Sigla | Quantidade | Conteúdo |
|---|---|---|---|
| **Global Descriptor Table** | GDT | 1 (única) | Segmentos acessíveis por todos os programas |
| **Local Descriptor Table** | LDT | Uma por programa | Área de memória de cada programa |
| **Interrupt Descriptor Table** | IDT | 1 (única) | Handlers de [[Interrupção|interrupções]] e exceções |

O endereço e limite de cada tabela são armazenados nos registradores [[Registradores x86#Registradores de Gerenciamento de Memória|GDTR, LDTR e IDTR]].

---

## Seletor de Segmento

No modo protegido (não usado no modo de 64 bits), os registradores de segmento usam um formato de **16 bits** dividido em três campos:

```
 15                    3    2    1 0
┌──────────────────────┬────┬──────┐
│     Índice (13 bits) │ TI │ RPL  │
└──────────────────────┴────┴──────┘
```

| Campo | Bits | Descrição |
|---|---|---|
| **RPL** (Requestor Privilege Level) | 2 | Nível de privilégio de quem acessa (0–3) |
| **TI** (Table Indicator) | 1 | 0 = usa GDT; 1 = usa LDT |
| **Índice** | 13 | Número da entrada na tabela (0–8.191) |

**Cálculo do endereço do descritor:**
```
offset = Índice × 8
endereço = base do GDTR (ou LDTR) + offset
```

### Cache do Seletor de Segmento

Quando um valor é carregado em um registrador de segmento, o processador:
1. Calcula o offset do descritor na tabela
2. Lê os 8 bytes (ou 16 no modo 64 bits) do descritor da RAM
3. Armazena esses dados em uma **memória interna** chamada **cache do seletor de segmento**

Cada registrador de segmento (CS, DS, SS, ES, FS, GS), LDTR e TR tem seu próprio cache. Esta é a parte **"invisível"** do seletor — o programador não acessa diretamente, mas o processador a usa para evitar releituras desnecessárias da RAM, aumentando o desempenho.

> [!warning] Diferente da cache de memória
> O cache do seletor de segmento é uma memória interna específica do mecanismo de segmentação — **não tem relação** com a [[Cache|cache L1/L2/L3]] do processador.

---

## Descritor de Segmento

Estrutura de **8 bytes** (modo protegido) que define completamente um segmento:

| Campo | Bits | Descrição |
|---|---|---|
| **Tamanho do segmento** | 20 | Tamanho do segmento (em bytes ou páginas, conforme G) |
| **Endereço base** | 32 | Endereço onde o segmento começa |
| **G** (Granularidade) | 1 | 0 = tamanho em bytes (1 B–1 MiB); 1 = tamanho em páginas de 4 KiB (4 KiB–4 GiB) |
| **D/B** (Default/Big) | 1 | 0 = modo 16 bits; 1 = modo 32 bits |
| **0/L** | 1 | 0 = modo protegido / compat.; 1 = modo de 64 bits |
| **AVL** (Available) | 1 | Disponível para uso pelo SO |
| **P** (Present) | 1 | 1 = segmento existe/está presente |
| **DPL** (Descriptor Privilege Level) | 2 | Nível de privilégio mínimo para acessar (0–3) |
| **S** (System) | 1 | 0 = segmento especial do SO |
| **C/D** (Code/Data) | 1 | Indica se é segmento de código ou dados |
| **E** (Expansion direction) | 1 | 0 = expande para cima (programas); 1 = expande para baixo ([[Pilha]]) |
| **W** (Writable) | 1 | 1 = escrita permitida |
| **A** (Accessed) | 1 | 1 = segmento já foi acessado |

### Descritor no modo de 64 bits

No modo de 64 bits, o endereço base é de **64 bits** em vez de 32. Como um descritor de 8 bytes não tem espaço suficiente, o processador usa **dois descritores consecutivos** (16 bytes no total). O segundo descritor armazena os bytes 5–8 do endereço base.

---

## Ver também
- [[Organização de Memória x86]] — endereçamento, segmentação, paginação
- [[Modos de Operação x86]] — modo protegido, modo real, modo de 64 bits
- [[Registradores x86]] — registradores GDTR, LDTR, IDTR, TR, CS/DS/SS/ES/FS/GS
- [[Memória Virtual]] — arquivo de troca (swap) e paginação
- [[Sistema Operacional]] — kernel opera em ring 0; drivers em ring 1/2
- [[Interrupção]] — IDT contém os handlers de interrupções
- [[Processo]] — cada processo tem sua própria LDT no modelo multissegmentado
