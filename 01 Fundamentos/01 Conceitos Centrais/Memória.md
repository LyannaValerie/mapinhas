---
title: Memória
aliases:
  - RAM
  - ROM
  - memória principal
  - M
tags:
  - computação/fundamentos
  - computação/arquitetura
date: 2026-04-06
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Fundamentos]]

# Memória

## Definição
Componente do [[Modelo de Von Neumann]] com duas funções distintas: armazenamento temporário para execução de instruções e armazenamento de programas. Em computadores pessoais modernos, corresponde à **RAM** e à **ROM**.

## Relações (SPO)
- Memória → armazena → [[Programa Armazenado]]
- Memória → armazena → [[Dados Binários]] (instruções e dados)
- Memória → acessada por → [[Processador]]
- Memória → componente de → [[Modelo de Von Neumann]]
- Memória → equivale a → RAM / ROM em PCs modernos

## Volatilidade

> [!warning] RAM é volátil
> O conteúdo da memória (RAM) é **apagado quando o computador é desligado**. Por isso, dados e programas que devem persistir ficam armazenados em [[Memória de Massa]] e são transferidos para a RAM somente quando necessários.

## As Duas Funções da Memória (Von Neumann)

### 1. Armazenamento temporário para execução
Área de trabalho do [[Processador]] durante instruções complexas (multiplicações, divisões). Como essas instruções se dividem em várias etapas, a memória guarda **resultados parciais** entre uma etapa e a próxima.

> [!info] Em computadores modernos
> Esta função de armazenamento temporário foi absorvida pelo próprio [[Processador]], através dos **registradores** internos — fisicamente mais próximos e, portanto, mais rápidos.

### 2. Armazenamento de programas e dados
Guarda a sequência de instruções ([[Programa Armazenado]]) e os dados que o [[Processador]] acessa para executar.

> [!note] Terminologia Von Neumann
> Na nomenclatura original: **instruções** eram chamadas de *ordens* e **programa** era chamado de *código*.

## Integração ao Processador

> [!tip] Tendência de integração
> Von Neumann previu que todos os componentes poderiam, em tese, ser integrados dentro do [[Processador]]. Em computadores modernos, a memória ainda reside em circuito externo — por limitações físicas de tamanho e fabricação. Porém, em processadores de uso específico (não PCs), essa integração total já existe.
>
> A proximidade física entre componentes aumenta o desempenho: encurta o caminho de comunicação, permitindo maior velocidade.

## Implementação física — DRAM

A memória principal (RAM) é fisicamente implementada com chips de **DRAM** (*Dynamic Random Access Memory* — Memória de Acesso Aleatório Dinâmica).

> Para tecnologias detalhadas (DDR, LPDDR, GDDR, HBM, módulos, largura de banda, latência): [[RAM — Tecnologias e Módulos]]

Logicamente, a memória é organizada como um **vetor linear de bytes**, cada um com um **endereço único** — um índice inteiro começando em zero:

```
Endereço:   0     1     2     3     4    ...    N-1
Conteúdo: [byte][byte][byte][byte][byte] ... [byte]
```

Cada instrução de máquina pode ocupar um número variável de bytes. Os tipos de dados do C têm tamanhos fixos, que variam por arquitetura:

| Tipo C | Tamanho em x86-64 (Linux) |
|---|---|
| `short` | 2 bytes |
| `int` | 4 bytes |
| `float` | 4 bytes |
| `long` | 8 bytes |
| `double` | 8 bytes |
| ponteiro (`*`) | 8 bytes |

## Distinção Memória vs Armazenamento

> [!warning] Confusão comum
> **Memória ≠ Armazenamento.** São blocos distintos no [[Modelo de Von Neumann]] (M ≠ R).
>
> | | Memória (M) | Armazenamento (R) |
> |---|---|---|
> | Exemplos | RAM, ROM | HD, SSD, pen drive |
> | Função | Execução ativa de programas | Persistência de dados |
> | Acesso pelo processador | Direto | Indireto — requer transferência para M antes |
>
> Atenção: SSDs e pen drives são construídos com chips de memória internamente, mas sua **função** é de armazenamento. São bloco R, não M.

## ROM e Inicialização

A [[Memória|RAM]] é volátil: ao ligar o computador, está completamente vazia e o [[Processador]] não sabe o que fazer. Para resolver isso, os computadores possuem uma memória de **somente leitura (ROM)** que não perde seu conteúdo sem energia.

A ROM contém o [[Firmware]] — um programa gravado de forma permanente que instrui o processador nos primeiros momentos após o liga. Em PCs modernos este firmware é o **UEFI**; em PCs antigos era o **BIOS**.

### Sequência de boot
```
1. ROM entrega instruções ao processador (firmware UEFI/BIOS)
2. Firmware executa o POST (Power On Self Test)
3. Firmware instrui carregamento do SO do armazenamento → RAM
4. Processador executa o SO (já na RAM)
```

Ver [[Firmware]] para detalhes sobre UEFI, BIOS e POST.

## Endereços de memória

A memória é dividida em **endereços** numerados a partir de zero até o limite máximo instalado. Por razões históricas, cada posição de memória (endereço) armazena um dado de **8 bits** — por isso a capacidade de memória continua sendo medida em **bytes**. Dados maiores que 1 byte ocupam múltiplos endereços consecutivos.

Ver [[Organização de Memória x86]] para endianness (little-endian), alinhamento, segmentação e paginação.

> [!note] Confusão comum: memória vs. armazenamento
> É comum usuários se referirem ao disco rígido ou SSD como "memória". Tecnicamente, **memória = RAM**. Discos, SSDs e pen drives são **dispositivos de armazenamento de dados** — o processador só consegue executar instruções que estejam na RAM; qualquer programa armazenado em disco precisa ser transferido para a RAM antes de ser executado.

## Ver também
- [[Processador]]
- [[Programa Armazenado]]
- [[Dados Binários]]
- [[Modelo de Von Neumann]]
- [[Meio Externo de Gravação]]
- [[DMA]]
- [[Firmware]] — UEFI/BIOS/POST/boot
- [[Tipos de Dados C]] — tamanhos de tipos C em perspectiva de programador (truncamento, promoção)
