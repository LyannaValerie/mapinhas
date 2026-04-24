---
title: ISA
aliases:
  - Instruction Set Architecture
  - arquitetura do conjunto de instrução
  - nível ISA
  - nível 2
tags:
  - computação/arquitetura
date: 2026-04-06
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# ISA — Instruction Set Architecture

## Definição
Nível 2 da [[Níveis de Abstração|organização estruturada de computadores]]. Define o conjunto de instruções de máquina que os fabricantes publicam nos manuais de referência ("linguagem de máquina" do ponto de vista do programador de sistemas). Suas instruções são executadas pelo microprograma ou circuitos do nível 1.

## Relações (SPO)
- ISA → é o nível 2 de → [[Níveis de Abstração]]
- ISA → executada por → microprograma ou hardware do nível 1 (microarquitetura)
- ISA → descrita em → manuais de referência do fabricante
- ISA → base para → [[Linguagem Assembly]] (nível 4)
- ISA → linguagem numérica → usada por programadores de sistemas, não de aplicações

## Mapa simbólico
```mapa
# Nível de abstração
ISA ≺ [[Níveis de Abstração]]                       : nível 2
[[Linguagem Assembly]] ≺ ISA                        : nível 4 sobre nível 2
ISA ≺ Microarquitetura                              : executada por nível 1

# Taxonomia de famílias
x86 ∈ CISC
ARM ∈ RISC
AVR ∈ RISC
CISC ∨ RISC ⊑ ISA

# Relações funcionais
ISA ⇒ [[Linguagem Assembly]]                        : base para
ISA ⊳ Compilador                                    : contrato
ISA ⊗ {ModeloDeMemória, Registradores, [[Tipos de Dados ISA]], [[Formatos de Instrução]], [[Modos de Endereçamento]]}
```

> [!note] Legenda rápida
> `≺` nível abaixo · `∈` pertence a · `⊑` subtipo · `⊗` composto por. Ver [[Linguagem Simbólica|MAPA]].

## Contexto

> [!info] O que os manuais de "linguagem de máquina" descrevem
> Quando um fabricante publica um manual intitulado "Manual de Referência da Linguagem de Máquina", ele está descrevendo o nível ISA — não os circuitos físicos subjacentes. Se o fabricante oferecer dois interpretadores (dois ISAs diferentes) para a mesma máquina física, precisará publicar dois manuais distintos.

## Relação com os outros níveis

- **Abaixo (nível 1):** microarquitetura — [[Registrador|registradores]], [[ULA]] e caminho de dados que interpretam as instruções ISA
- **Acima (nível 3):** sistema operacional — nível híbrido onde algumas instruções ISA passam diretamente e outras são interceptadas pelo SO
- **Acima (nível 4):** [[Linguagem Assembly]] — representação simbólica da ISA

## As três famílias de ISA mais relevantes

| ISA             | Tipo                  | Mercado dominante                        | Uso neste estudo                                                              |
| --------------- | --------------------- | ---------------------------------------- | ----------------------------------------------------------------------------- |
| **x86**         | [[RISC e CISC|CISC]] | PCs (Windows, Linux, Mac) e servidores   | Arquitetura de referência para PCs — ver [[Intel x86]]                        |
| **ARM**         | [[RISC e CISC|RISC]] | Smartphones e tablets (maioria absoluta) | Mercado móvel — ver [[ARM]]                                                   |
| **AVR** (Atmel) | [[RISC e CISC|RISC]] | Microcontroladores de baixo custo        | Computação embutida (ex: [[Microcontrolador#Arduino|Arduino]]) — ver [[AVR]] |

> [!info] Por que três ISAs?
> Cada família representa um ponto distinto no espectro de trade-offs (desempenho × custo × consumo). x86 maximiza desempenho; ARM equilibra desempenho e energia; AVR minimiza custo.

## Interface Compilador–Hardware
ISA é o contrato entre quem escreve compiladores e quem projeta hardware. Compiladores produzem código ISA; o hardware executa esse código. A figura abaixo resume a relação:

```
Código FORTRAN ──┐
Código C ────────┼──► compilação ──► Código ISA ──► Hardware (microarquitetura)
Código Java ─────┘
```

> [!info] Por que a ISA importa
> Uma boa ISA pode representar até 25% de diferença de desempenho em relação a uma ruim, mesmo com implementações equivalentes. Dois fatores determinam qualidade: (1) executável com eficiência em tecnologias atuais e futuras; (2) alvo claro para compiladores — regularidade e completude das opções.

## Propriedades do Nível ISA

### Definição formal
Muitas ISAs têm **documento normativo** publicado por consórcio ou fabricante. Objetivo: múltiplos fabricantes produzem chips que executam o mesmo software.

- **Seções normativas:** usam "deve", "não pode", "deveria" → requisitos obrigatórios vs. sugestões
- **Seções informativas:** ajudam o leitor mas não fazem parte da definição formal
- Exemplo: "Executar opcode reservado *deverá* causar exceção" → comportamento obrigatório; "efeito é definido pela implementação" → liberdade ao fabricante

### O que define o nível ISA
O que um compilador precisa saber para produzir código correto:
- Modelo de memória (tamanho de célula, endianness, alinhamento, semântica)
- Registradores disponíveis (tipo, quantidade, largura)
- [[Tipos de Dados ISA]] suportados
- [[Formatos de Instrução]] e [[Modos de Endereçamento]]
- Comportamento de cada instrução

O que **não** faz parte do nível ISA (invisível ao compilador em teoria):
- Microprogramação ou não
- Paralelismo interno
- Profundidade de pipeline
- Número de caches

> [!warning] Exceção: desempenho é visível
> Arquiteturas superescalares com emissão dupla expõem seu comportamento ao compilador (ex: alternar inteiro e FP para melhor throughput). A separação entre camadas não é perfeita.

## Modos de Operação

| Modo | Acesso | Uso |
|---|---|---|
| **Núcleo (kernel)** | Todas as instruções permitidas | Sistema operacional |
| **Usuário (user)** | Instruções sensíveis bloqueadas | Programas de aplicação |

Instruções sensíveis bloqueadas em modo usuário: manipulação direta de cache, alteração de registradores de controle, halt da CPU, modificação do PSW.

> [!example] Core i7 — três modos
> - **Modo real:** comportamento idêntico ao 8088; sem proteção; qualquer erro trava a máquina
> - **Modo virtual 8086:** executa programas 8088 antigos em ambiente protegido (janela MS-DOS no Windows)
> - **Modo protegido:** 4 níveis de privilégio (0 = núcleo, 3 = usuário); acesso total apenas no nível 0

## Modelo de Memória

### Tamanho de célula e endianness
- Célula padrão: **8 bits** (byte / octeto) — encaixa 1 caractere ASCII
- Palavras: agrupamentos de 4 bytes (32 bits) ou 8 bytes (64 bits)
- **Little-endian** (x86): byte menos significativo no endereço menor
- **Big-endian / bi-endian** (ARM): configurável por bloco de memória lido na inicialização

### Alinhamento
Algumas arquiteturas exigem que palavras comecem em múltiplos do seu tamanho:

| Tipo | Alinhamento exigido |
|---|---|
| Palavra de 4 bytes | Endereço múltiplo de 4 |
| Palavra de 8 bytes | Endereço múltiplo de 8 |

- Acesso não alinhado no x86: possível, mas exige duas leituras de memória + montagem → penalidade de desempenho
- Core i7: interface DDR3 admite apenas acessos alinhados em 64 bits; hardware faz a fusão de duas leituras quando necessário

### Espaço de endereçamento
- **Único (padrão):** instruções e dados compartilham o mesmo espaço (0 a 2³²−1 ou 2⁶⁴−1)
- **Separado (instruções / dados):** dois espaços independentes; vantagens:
  1. Dobra o espaço endereçável com endereços de mesmo tamanho
  2. Impossibilita sobrescrever o programa por acidente
  3. Dificulta ataques de malware que tentam modificar o código em execução

> [!note] Cache dividida ≠ espaços separados
> Cache L1 dividida (instrução + dado) ainda usa um único espaço de endereço — apenas armazena partes distintas desse espaço em caches diferentes.

### Semântica de memória
Em sistemas com reordenação de instruções ou múltiplas CPUs, um `LOAD` logo após um `STORE` no mesmo endereço pode não retornar o valor recém-escrito se as operações forem reordenadas pelo hardware.

| Estratégia | Desempenho | Semântica |
|---|---|---|
| Serialização total | Baixo | Simples: ordem estrita do programa |
| Instrução SYNC explícita | Alto | Compilador controla; programador deve inserir barreiras |
| Bloqueio seletivo (RAW/WAR) | Intermediário | Hardware bloqueia dependências críticas |

## Registradores

### Categorias
- **Uso geral:** variáveis locais e resultados intermediários; acesso rápido sem ir à memória
- **Uso especial:** PC (contador de programa), SP (ponteiro de pilha), FP (ponteiro de quadro), PSW

### PSW — Program Status Word
Registrador híbrido núcleo/usuário. Contém **códigos de condição** ajustados pela ULA a cada operação:

| Flag | Condição |
|---|---|
| **N** | Resultado Negativo |
| **Z** | Resultado Zero |
| **V** | Transbordo (overflow) |
| **C** | Vai-um (carry) do bit mais significativo |
| **A** | Vai-um auxiliar (bit 3 → 4); usado em BCD |
| **P** | Paridade par |

Instruções de comparação (`CMP`) ajustam esses bits; instruções de desvio condicional (`BEQ`, `BLT`...) os testam.

O PSW também contém: modo da máquina (usuário/núcleo), bit de rastreamento (debug), nível de prioridade, estado de habilitação de interrupções.

> [!note] Convenções de uso de registradores
> Mesmo quando registradores são intercambiáveis, sistemas operacionais e compiladores adotam convenções (ABI): quais passam parâmetros, quais são preservados por chamadas, quais são transitórios. Violar a ABI gera corrupção silenciosa.

## Compatibilidade Retroativa
Pressão de mercado: nova máquina deve executar todos os programas da antecessora sem modificação.

- Permite trocar microarquitetura (microprogramação → execução direta, superescalar, out-of-order) sem quebrar software
- Torna difícil remover decisões ruins do ISA (ex: segmentação do x86, irregularidade dos registradores do 8086)
- ISA ruim pode custar até 25% de desempenho vs. ISA equivalente bem projetada

## Ver também
- [[Níveis de Abstração]]
- [[Linguagem de Máquina]]
- [[Linguagem Assembly]]
- [[RISC e CISC]]
- [[Registrador]]
- [[ULA]]
- [[Processador]]
- [[Microcontrolador]]
- [[Modos de Endereçamento]]
- [[Formatos de Instrução]]
- [[Tipos de Dados ISA]]
- [[Flag]]
- [[Interrupção]]
