---
title: Organização de Memória x86
aliases:
  - little-endian
  - big-endian
  - alinhamento de memória
  - alignment
  - endereçamento segmentado
  - segmento:offset
  - endereçamento linear
  - modelo plano
  - modelo multissegmentado
  - paginação
  - página de memória
  - endereço físico
  - endereço linear
  - endereço lógico
tags:
  - computação/arquitetura
date: 2026-04-09
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Organização de Memória x86

## Definição
Como a arquitetura [[Intel x86]] organiza o acesso à [[Memória|RAM]]: endereçamento por byte, ordem de armazenamento (endianness), alinhamento de dados e os sistemas de endereçamento linear e segmentado.

---

## Endereçamento por byte

Por razões históricas, cada endereço de memória armazena **8 bits (1 byte)**. Por isso a capacidade de memória é sempre expressa em bytes.

Consequência: dados maiores que 1 byte ocupam **múltiplos endereços consecutivos**:

| Tipo de dado | Bytes | Endereços |
|---|---|---|
| byte | 1 | 1234h |
| word (16 bits) | 2 | 1234h, 1235h |
| dword (32 bits) | 4 | 1234h … 1237h |
| qword (64 bits) | 8 | 1234h … 123Bh |

---

## Ordem de Armazenamento — Little-Endian

Processadores x86 usam a convenção **little-endian**: os bytes são armazenados na memória do **menos significativo para o mais significativo**.

```
Valor 32 bits: 0x12345678
Endereço:  1234h  1235h  1236h  1237h
Conteúdo:   78     56     34     12
            (LSB)               (MSB)
```

> [!note] Big-endian
> Algumas arquiteturas (ex: SPARC, PowerPC clássico) usam big-endian — o byte mais significativo vem primeiro. Redes IP usam big-endian, o que exige conversão em comunicações com processadores x86.

---

## Sobreposição de Endereços

Como um dado de N bytes ocupa N endereços consecutivos, o mesmo byte físico pode ser referenciado por diferentes endereços base dependendo do tamanho do acesso:

```
Endereço:   1234h  1235h  1236h  1237h  1238h ...
           [DADO1][DADO2][DADO3][DADO4][DADO5]...

MOV EAX, [1234h] → lê DADO1+DADO2+DADO3+DADO4
MOV EAX, [1235h] → lê DADO2+DADO3+DADO4+DADO5
```

DADO2, DADO3, DADO4 aparecem em ambas as leituras — em posições diferentes dentro da word de 32 bits resultante.

---

## Alinhamento de Dados

**Problema:** acessar dados em endereços não alinhados desperdiça ciclos — o processador pode precisar de dois acessos à memória para montar o dado completo.

**Solução:** acessar sempre em endereços que sejam **múltiplos do tamanho do dado**:

| Tamanho do dado | Endereço alinhado deve ser múltiplo de |
|---|---|
| 16 bits (2 bytes) | 2 (endereço par) |
| 32 bits (4 bytes) | 4 |
| 64 bits (8 bytes) | 8 |

> [!example] Acesso alinhado vs. desalinhado (32 bits)
> - **Alinhado:** `MOV EAX, [1234h]` — endereço 1234h é múltiplo de 4 ✓
> - **Desalinhado:** `MOV EAX, [1235h]` — 1235h não é múltiplo de 4; o processador precisará de dois acessos para montar os 4 bytes

> [!tip] Compiladores e alinhamento
> Compiladores modernos inserem automaticamente **padding** (bytes de preenchimento) em structs e arrays para garantir alinhamento. É por isso que o tamanho de uma struct em C pode ser maior que a soma dos seus campos.

---

## Representação de Endereços

### Endereçamento Linear
Os endereços são numerados **sequencialmente** a partir de zero. É o modo usado por programas no modo protegido e no modo de 64 bits. Simples e direto:

```
Endereço físico: 0x001F3A0 → acessa diretamente aquele byte
```

### Endereçamento Segmentado (Modo Real)
Criado no 8086 para **reduzir o tamanho das instruções** em uma época de RAM muito limitada. A memória é dividida em segmentos, e os endereços usam o formato **segmento:offset**.

#### Cálculo do endereço físico

```
Endereço físico = (Segmento × 16) + Offset
               = (Segmento << 4) + Offset
```

O segmento é deslocado 4 bits à esquerda (equivalente a adicionar um `0` hexadecimal à direita), depois soma-se o offset de 16 bits.

| Notação | Cálculo | Endereço físico |
|---|---|---|
| `1F00:03A0` | `1F000 + 03A0` | `1F3A0h` |
| `0510:0123` | `05100 + 0123` | `05223h` |
| `4A29:547B` | `4A290 + 547B` | `4F70Bh` |

> [!note] 4.096 combinações por endereço físico
> Como o endereço físico resulta de uma soma, há **4.096 combinações** de segmento:offset que apontam para o mesmo byte. Exemplo: `1F00:03A0`, `1F3A:0000`, `1E00:13A0` e `1DF0:14A0` são todos equivalentes a `1F3A0h`.

#### Limites do modo real
- **Barramento de endereços:** 20 bits → máximo de **1 MiB** (2²⁰ bytes)
- **Offset:** 16 bits → cada segmento tem até **64 KiB** (2¹⁶ bytes)

#### Exemplo de acesso segmentado (assembly x86)

```asm
mov ax, 1f00h   ; carrega segmento em AX
mov ds, ax      ; transfere para registrador de segmento DS
mov al, [03a0h] ; lê byte no offset 03A0h do segmento DS (= endereço 1F3A0h)
```

Comparado ao modo linear: `mov al, [1f3a0h]` — muito mais simples.

**Por que segmentação?** Com o segmento já carregado em DS, instruções subsequentes precisam fornecer apenas 16 bits de offset em vez de 20 bits de endereço completo. Em uma época de RAM escassa, cada byte economizado importava.

---

## Organização da Memória no Modo Protegido

O modo protegido oferece três modelos de acesso à memória:

### 1. Modelo Plano Básico (Flat Basic)
Memória acessada como uma entidade única, sem segmentação. Simples, sem proteção adicional.

### 2. Modelo Plano Protegido (Flat Protected)
Igual ao anterior, mas o processador gera uma **exceção** se tentar acessar um endereço além da memória fisicamente instalada.

### 3. Modelo Multissegmentado
O modelo padrão do modo protegido. A memória é dividida em **segmentos de tamanho variável**, e cada segmento pode ser "protegido" — associado a um programa específico:

- Se um programa tenta acessar a área de outro programa → **exceção gerada**
- Permite múltiplos programas em memória simultaneamente sem interferência

### Paginação e Endereços Lógicos vs. Físicos

No modo protegido (e de 64 bits), os segmentos são subdivididos em **páginas** de tipicamente **4 KiB** cada. Cada página pode estar:
- Na **RAM** (acesso direto)
- Em um **arquivo de troca** (swap file) no armazenamento de massa → técnica de [[Memória Virtual]]

Quando a paginação está ativa (o padrão nos sistemas modernos):

```
Programa usa → endereço linear (lógico)
                        ↓  [Unidade de Paginação]
              endereço físico (posição real na RAM)
```

A **unidade de paginação** do processador converte automaticamente endereços lógicos em físicos. Os registradores [[Registradores x86#Registradores de Controle (CR)|CR3]] (endereço da tabela de páginas) e **CR2** (endereço da última page fault) são a interface entre o hardware de paginação e o SO.

Ver [[Memória Virtual]] para a perspectiva do sistema operacional (espaço de endereçamento por processo, page faults, heap/stack).

---

## Ver também
- [[Memória]] — conceito geral e organização linear por byte
- [[Memória Virtual]] — paginação, espaço virtual por processo, page fault
- [[Proteção de Memória x86]] — tabelas GDT/LDT/IDT, descritores de segmento, rings de privilégio
- [[Paginação x86]] — tabelas de páginas, TLB, modos 32/PAE/4 níveis/5 níveis
- [[Modos de Operação x86]] — modo real, protegido, de 64 bits
- [[Registradores x86]] — registradores de segmento (CS/DS/SS/ES/FS/GS), CR2, CR3
- [[Pilha]] — a pilha usa o espaço de endereçamento normal
- [[Linguagem Assembly]] — instruções de acesso a memória com notação segmento:offset
- [[Dados Binários]] — representação de bytes e words
