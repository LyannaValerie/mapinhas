---
title: Intel x86
aliases:
  - família x86
  - arquitetura x86
  - Intel
  - histórico Intel
tags:
  - computação/arquitetura
  - computação/histórico
date: 2026-04-06
---

# Intel x86

## Definição
Família de [[ISA|arquiteturas de conjunto de instruções]] compatíveis entre si, criada pela Intel, que evoluiu do microprocessador de 4 bits 4004 (1971) até os processadores multicore modernos. Domina o mercado de [[PC|PCs]] e servidores. Ver [[ISA#As três famílias de ISA mais relevantes]].

## Fundação da Intel
- **1968:** Robert Noyce (inventor do CI), Gordon Moore ([[Lei de Moore]]) e Arthur Rock fundam a Intel para fabricar chips de **memória**
- Primeiro ano: $3.000 em vendas
- Hoje: maior fabricante de chips de CPU do mundo

### Origem do 4004 — caso Busicom
- **Setembro de 1969:** empresa japonesa **Busicom** encomenda 12 chips sob medida para uma calculadora eletrônica
- **Ted Hoff** (engenheiro Intel designado ao projeto) percebe que uma CPU de 4 bits de uso geral em um único chip seria mais simples e mais barata
- **1970:** nasce o **4004** — 2.300 transistores, primeira CPU de um chip
- Nem a Intel nem a Busicom perceberam a importância do que tinham criado
- Intel propôs recomprar os direitos do 4004 por **$60.000** (valor original pago pela Busicom) → oferta aceita de imediato
- Com os direitos em mãos, a Intel iniciou o projeto do **8008** (versão de 8 bits)

## Evolução dos chips (tabela)

| Chip | Ano | MHz | Transistores | Memória | Marco |
|---|---|---|---|---|---|
| **4004** | 1971 | 0,108 | 2.300 | 640 B | Primeiro microprocessador em um chip (4 bits) |
| **8008** | 1972 | 0,108 | 3.500 | 16 KB | Primeiro de 8 bits |
| **8080** | 1974 | 2 | 6.000 | 64 KB | Primeiro de uso geral; vendido aos milhões |
| **8086** | 1978 | 5–10 | 29.000 | 1 MB | Primeiro de 16 bits |
| **8088** | 1979 | 5–8 | 29.000 | 1 MB | Usado no IBM PC original |
| **80286** | 1982 | 8–12 | 134.000 | 16 MB | Proteção de memória |
| **80386** | 1985 | 16–33 | 275.000 | 4 GB | Primeiro de **32 bits** |
| **80486** | 1989 | 25–100 | 1,2 M | 4 GB | FPU + [[Cache]] L1 (8 KB) + suporte multiprocessador |
| **Pentium** | 1993 | 60–233 | 3,1 M | 4 GB | Dois [[Pipeline|pipelines]]; depois MMX |
| **Pentium Pro** | 1995 | 150–200 | 5,5 M | 4 GB | 5 instruções/ciclo; [[Cache]] L1+L2 |
| **Pentium II** | 1997 | 233–450 | 7,5 M | 4 GB | Pentium Pro + MMX |
| **Pentium III** | 1999 | 650–1.400 | 9,5 M | 4 GB | Instruções SSE (gráficos 3D) |
| **Pentium 4** | 2000 | 1.300–3.800 | 42 M | 4 GB | Hyperthreading; SSE expandido |
| **Core Duo** | 2006 | 1.600–3.200 | 152 M | 2 GB | Dual core em um substrato |
| **Core** | 2006 | 1.200–3.200 | 410 M | 64 GB | Quad core de 64 bits |
| **Core i7** | 2011 | 1.100–3.300 | 1.160 M | 24 GB | GPU integrada; [[Cache]] L3 compartilhada |

## Marcos arquiteturais chave

### 8080 e a herança do PDP-8
O 8080 (1974) foi descrito como "muito parecido com o [[História da Computação|PDP-8]]" — o minicomputador de massa da DEC lançado em 1965. Onde o PDP-8 vendeu dezenas de milhares de unidades, a Intel vendeu **milhões** do 8080. Conexão histórica: o mesmo salto de escala que a DEC fez ao popularizar os minicomputadores, a Intel repetiu com os microprocessadores.

### Barramento de 8 vs. 16 bits — 8086/8088
O 8088 tinha a **mesma arquitetura e conjunto de instruções** do 8086, mas barramento externo de 8 bits (vs. 16 bits do 8086) — tornando-o mais lento e mais barato. A IBM escolheu o 8088 para o IBM PC original, tornando-o padrão da indústria.

### 80286 — compatibilidade ao custo da elegância
O 80286 mantinha o mesmo conjunto de instruções do 8086/8088, mas sua organização de memória foi descrita como "um pouco desajeitada" devido ao requisito de compatibilidade com chips anteriores. Foi usado no **IBM PC/AT** e nos modelos de faixa média **PS/2**.

### Compatibilidade retroativa total
Todo chip Intel executa programas do 8086 sem modificação. Essa compatibilidade foi requisito de projeto constante — ao custo de uma arquitetura progressivamente menos elegante.

### Pipeline (Pentium)
Dois pipelines internos no Pentium — aproximadamente 2× mais rápido que o 486. Ver [[Pipeline]] para detalhes.

### Cache embutida (80486 em diante)
- **80486:** 8 KB de [[Cache]] L1 no chip
- **Pentium Pro:** L1 (8 KB instruções + 8 KB dados) + L2 (256 KB no mesmo pacote)
- **Core i7:** L1 e L2 por núcleo + L3 compartilhada entre todos os núcleos

### Extensões de multimídia
- **MMX** (MultiMedia eXtension) — aceleração de áudio e vídeo; elimina necessidade de coprocessadores
- **SSE** (Streaming SIMD Extensions) — gráficos 3D (Pentium III)
- Versões posteriores: SSE2, SSE3, SSE4

### Hyperthreading (Pentium 4)
Permite distribuir trabalho em **dois threads** executados em paralelo por um único core físico — precursor do multicore.

### Multicore (Core Duo em diante)
Dois ou mais CPUs no mesmo chip. Motivação técnica:
- O consumo de energia é proporcional ao **quadrado da tensão**
- Duas CPUs a velocidade X consomem muito menos que uma CPU a velocidade 2X
- Pentium 4 de 3,6 GHz = 115 W ≈ lâmpada de 100 W
- Intel cancelou o Pentium 4 de 4 GHz em novembro de 2004 por dissipação de calor

### Core i7 — núcleos físicos vs. habilitados
O chip Core i7-3960X possui **8 núcleos físicos**, mas — exceto na versão Xeon — **apenas 6 são habilitados**. Técnica deliberada:
- Chips com 1 ou 2 núcleos defeituosos ainda são vendidos com os núcleos ruins **desabilitados**
- Resultado: maior aproveitamento do silício fabricado, reduzindo descarte e custo total
- Cada núcleo tem [[Cache]] L1 e L2 próprias; há uma [[Cache]] L3 **compartilhada** entre todos os núcleos

### Variantes de mercado
| Variante | Mercado | Diferencial |
|---|---|---|
| Celeron | PCs de baixo custo | Cache reduzida |
| Xeon | Servidores / workstations | Cache maior; mais núcleos; melhor suporte multiprocessador |
| Pentium M / Centrino | Notebooks (2003) | Baixo consumo; menor e mais leve; WiFi (IEEE 802.11) embutido; metas: bateria + mobilidade |

## Origem do nome "Pentium"
A Intel descobriu (perdendo processo judicial) que números não podem ser marca registrada — portanto "80586" não era registrável. Escolheu "Pentium" (do grego πεντε = cinco). "Sexium" (sex = seis em latim) foi descartado pelo marketing em favor de manter o nome Pentium.

## Ver também
- [[Processadores x86 Famílias 1-4]] — detalhe de 8086/8088/286/386/486 com variantes e contexto histórico
- [[ISA]]
- [[RISC e CISC]]
- [[Cache]]
- [[Pipeline]]
- [[Processador]]
- [[Modos de Operação x86]] — real, protegido 32/64 bits, Virtual-8086, SMM
- [[Registradores x86]] — catálogo completo de todos os registradores
- [[Lei de Moore]]
- [[PC]]
- [[História da Computação]]
