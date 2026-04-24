---
title: História da Computação
aliases:
  - marcos da arquitetura
  - história dos computadores
  - evolução dos computadores
tags:
  - computação/histórico
date: 2026-04-06
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# História da Computação

## Marcos do Computador Digital Moderno

| Ano | Nome | Criador | Importância |
|---|---|---|---|
| 1834 | Máquina Analítica | Babbage | Primeira tentativa de computador digital de uso geral |
| 1936 | Z1 | Zuse | Primeira calculadora com relés |
| 1943 | COLOSSUS | Governo britânico | Primeiro computador eletrônico |
| 1944 | Mark I | Aiken | Primeiro computador norte-americano de uso geral |
| 1946 | ENIAC | Eckert/Mauchley | **A história moderna dos computadores começa aqui** |
| 1949 | EDSAC | Wilkes | Primeiro computador com [[Programa Armazenado]] executado |
| 1951 | Whirlwind I | MIT | Primeiro computador de tempo real |
| 1952 | IAS | Von Neumann | **A maioria das máquinas atuais usa esse projeto** |
| 1960 | PDP-1 | DEC | Primeiro minicomputador (50 unidades) |
| 1961 | 1401 | IBM | Grande popularidade em pequenos negócios |
| 1962 | 7094 | IBM | Dominou computação científica no início dos anos 1960 |
| 1963 | B5000 | Burroughs | Primeira máquina projetada para linguagem de alto nível |
| 1964 | 360 | IBM | Primeira linha projetada como uma família |
| 1964 | 6600 | CDC | Primeiro supercomputador científico |
| 1965 | PDP-8 | DEC | Primeiro minicomputador de massa (50 mil unidades) |
| 1970 | PDP-11 | DEC | Dominou os minicomputadores nos anos 1970 |
| 1974 | 8080 | Intel | Primeiro computador de uso geral de 8 bits em um chip |
| 1974 | CRAY-1 | Cray | Primeiro supercomputador vetorial |
| 1978 | VAX | DEC | Primeiro superminicomputador de 32 bits |
| 1981 | IBM PC | IBM | **Deu início à era moderna do [[PC]]** |
| 1981 | Osborne-1 | Osborne | Primeiro computador portátil ([[Notebook]]) |
| 1983 | Lisa | Apple | Primeiro [[PC]] com GUI |
| 1985 | 386 | Intel | Primeiro ancestral de 32 bits da linha Pentium — ver [[Intel x86]] |
| 1985 | ARM (ARM2) | Acorn | Primeiro RISC de 32 bits para uso pessoal; base dos SoCs móveis atuais — ver [[ARM]] |
| 1985 | MIPS | MIPS | Primeira máquina RISC comercial |
| 1985 | XC2064 | Xilinx | Primeiro FPGA |
| 1987 | SPARC | Sun | Primeira estação de trabalho RISC baseada em SPARC |
| 1989 | GridPad | Grid Systems | Primeiro computador tablet comercial |
| 1990 | RS6000 | IBM | Primeira máquina superescalar |
| 1992 | Alpha | DEC | Primeiro PC de 64 bits |
| 1992 | Simon | IBM | Primeiro smartphone |
| 1993 | Newton | Apple | Primeiro PDA (palmtop) |
| 2001 | POWER4 | IBM | Primeiro multiprocessador dual core |

---

## Quinta Geração — Computadores de Baixa Potência e Invisíveis

O projeto japonês oficial de 5ª geração (1981, $500M, IA) fracassou — análogo à Máquina Analítica de Babbage: visão à frente da tecnologia disponível.

A verdadeira 5ª geração aconteceu de forma inesperada: **os computadores encolheram**.

| Marco | Ano | Importância |
|---|---|---|
| GridPad | 1989 | Primeiro tablet; tela sensível, caneta, portátil |
| Simon (IBM) | 1992 | Primeiro smartphone; touchscreen + PDA + telefone + e-mail |
| Apple Newton | 1993 | Primeiro PDA; entrada por escrita à mão |
| Palm Pilot | ~1996 | PDA popular; entrada "Graffiti" (Jeff Hawkins) |
| Apple iPhone / Android | 2007+ | Smartphones de massa |

**Computadores embutidos (invisíveis):** processadores em eletrodomésticos, relógios, cartões bancários, automóveis. Hardware e software projetados em conjunto. Presentes desde os anos 1970, mas com influência crescente.

> [!info] Computação Ubíqua / Pervasiva
> Conceito de **Mark Weiser**: computadores por toda parte, embutidos em tudo — invisíveis, parte da estrutura da vida diária. Mudará o mundo com a mesma profundidade que a Revolução Industrial.

---

## Quarta Geração — VLSI (1980–?)

**VLSI (Very Large Scale Integration):** dezenas de milhares → milhões de transistores em um chip. Computadores passaram de centrais de computação → departamentos → **um único indivíduo**.

### Era do Computador Pessoal

| Marco | Ano | Importância |
|---|---|---|
| Intel 8080 (kits) | ~1975 | Primeiros PCs como kits de montagem; sem software incluso |
| CP/M | ~1977 | Primeiro SO popular para PCs; Gary Kildall |
| Apple I / II | ~1977 | Jobs e Wozniak; sucesso doméstico e escolar |
| **IBM PC** | **1981** | Intel 8088; planos publicados abertamente → explosão de clones |
| Osborne-1 | 1981 | Primeiro portátil comercial (11 kg) |
| Lisa (Apple) | 1983 | Primeiro PC com GUI; fracassou por custo |
| **Macintosh** | **1984** | GUI acessível; enorme sucesso; inspirou Windows |
| Compaq portátil | ~1982 | Primeiro clone portátil do IBM PC; liderou mercado de notebooks |

### Microsoft e Intel

- **IBM PC** com MS-DOS (Microsoft) → IBM publicou projeto abertamente → clones inundaram o mercado
- **OS/2** (IBM + Microsoft) → substituído pelo **Windows** (só Microsoft)
- Intel: 8088 → **80386** (1985, 32 bits) → 80486 → **Pentium** → **Core** → família **x86**
- **AMD**: chips compatíveis x86

### RISC, CISC e Superescalar

- **CISC:** arquiteturas complexas com muitas instruções (ex: x86) — dominaram a 4ª geração
- **RISC** (~1985): arquiteturas simplificadas, mais rápidas — ver [[RISC e CISC]]
- **Superescalar** (anos 1990): CPUs que executam **múltiplas instruções simultaneamente**, às vezes fora de ordem

### FPGA (Xilinx, 1985)

Chip com portas lógicas genéricas "programáveis" em campo (sem fábrica de silício). Hardware tão maleável quanto software. Popular em prototipagem, aplicações de baixo volume e educação.

### 64 bits e Multicore

- **DEC Alpha** (1992): primeiro PC de 64 bits; verdadeiro RISC
- **IBM POWER4** (2001): primeiro processador dual-core — dois processadores no mesmo chip
- Anos 2000+: multicore torna-se padrão em desktops e servidores
- Desafio: desempenho de multicore exige programação **explicitamente paralela** — difícil e propenso a erros

---

## Terceira Geração — Circuitos Integrados (1965–1980)

**[[Circuito Integrado]]** inventado independentemente por **Jack Kilby** e **Robert Noyce** em 1958 — dezenas de transistores em um único chip → computadores menores, mais rápidos e mais baratos.

### IBM System/360 (1964)
O IBM tinha duas máquinas de sucesso incompatíveis (7094: científica; 1401: comercial). A solução foi radical: uma **família** de máquinas com a mesma linguagem de montagem:

| Propriedade | Modelo 30 | Modelo 40 | Modelo 50 | Modelo 65 |
|---|---|---|---|---|
| Desempenho relativo | 1 | 3,5 | 10 | 21 |
| Ciclo (ns) | 1.000 | 625 | 500 | 250 |
| Memória máxima (bytes) | 65.536 | 262.144 | 262.144 | 524.288 |

**Inovações da 360:**
- **Família de computadores** — mesma ISA em modelos de diferentes portes; software compatível entre modelos
- **[[Multiprogramação]]** — vários programas na memória ao mesmo tempo; CPU ocupa o tempo de espera de E/S de um programa executando outro
- **[[Emulação]]** — microprogramas para imitar a 1401 e a 7094; clientes migraram sem reescrever software
- **Arquitetura híbrida:** 16 registradores de 32 bits (aritmética binária) + memória orientada a bytes (como a 1401)
- **Espaço de endereçamento:** 2²⁴ = 16 MB (imenso para a época; tornou-se gargalo anos depois)

> [!note] Evolução do endereçamento
> 360 → 24 bits → 32 bits (série 370 em diante) → 64 bits. Cada transição foi motivada pelo esgotamento do espaço de endereçamento anterior.

### Por que o modelo 75 era 50× mais rápido que o modelo 30 com apenas 5× o clock? (Q10)

A tabela acima mostra que o **modelo 65** já tinha ciclo 4× mais rápido que o modelo 30 (250 ns vs 1.000 ns). O **modelo 75** tinha clock ~5× mais rápido — mas desempenho **50×** maior. A discrepância se explica porque **clock é apenas um dos fatores de desempenho**:

| Fator | Modelo 30 | Modelo 75 |
|---|---|---|
| Ciclo de clock | Lento (referência) | ~5× mais rápido |
| Largura do caminho de dados | 8 bits por vez | 32/64 bits por vez |
| Unidades funcionais paralelas | Uma | Várias (ULA, ponto flutuante, E/S simultâneos) |
| Interleaving de memória | Não | Sim — múltiplos bancos acessados em paralelo |
| [[Cache]] e prefetch | Mínimo | Sofisticado |
| Pipeline | Ausente ou rudimentar | Presente |

> [!info] Conclusão
> Desempenho = clock × (instruções por ciclo) × (trabalho por instrução). O modelo 75 multiplicava todos os fatores simultaneamente — não apenas o clock. Essa é a razão pela qual especificações de clock sozinhas são enganosas para comparar desempenho real.

A série 360 foi a base de todas as séries IBM subsequentes: 370, 4300, 3080, 3090, 390, z.

### DEC PDP-11 (1970)
- Sucessor de 16 bits do PDP-8; registradores orientados a palavras + memória orientada a bytes
- Enorme sucesso nas universidades; consolidou liderança da DEC nos minicomputadores

---

## Segunda Geração — Transistores (1955–1965)

**[[Transistor]]** inventado no Bell Labs em 1948 por **John Bardeen, Walter Brattain e William Shockley** (Prêmio Nobel de Física, 1956). Ao final da década de 1950, computadores de válvulas estavam obsoletos.

### TX-0 / TX-2 (MIT Lincoln Lab)
- TX-0: primeiro computador transistorizado; 16 bits; inspirado no Whirlwind I
- Kenneth Olsen (engenheiro do TX-0) → fundou a **DEC** (Digital Equipment Corporation) em 1957

### PDP-1 (DEC, 1961)
- **4.096 palavras de 18 bits**; 200 mil instruções/segundo; custo: **$120.000** (vs. milhões do IBM 7090)
- Primeiro **minicomputador**; dezenas de unidades vendidas
- Primeiro **videogame da história**: Spacewar (estudantes do MIT)

### PDP-8 (DEC, ~1965)
- **12 bits**; custo: **$16.000**; **50.000 unidades vendidas**
- Inovação fundamental: **[[Barramento|omnibus]]** — barramento único conectando todos os componentes
- Ruptura com a arquitetura centrada na memória (IAS); adotada por quase todos os computadores pequenos desde então
- **Ponte:** o Intel **8080** (1974) foi descrito como "muito parecido com o PDP-8" — onde a DEC vendeu 50 mil unidades, a Intel vendeu milhões. Ver [[Intel x86]]

### IBM 7090 / 7094
- Versões transistorizadas do 709
- **7094:** ciclo de **2 microssegundos**; 32.768 palavras de 36 bits; dominaram computação científica nos anos 1960

### IBM 1401
- Máquina de negócios; grande sucesso comercial
- Sem registradores, sem comprimento de palavra fixo
- **4 KB** de memória; bytes de 8 bits (6 bits de dado + 1 bit administrativo + 1 bit de fim de palavra)

### CDC 6600 (1964, Seymour Cray)
- **~10× mais rápido** que o IBM 7094 — primeiro **supercomputador**
- Segredo: **paralelismo interno** — múltiplas unidades funcionais (adição, multiplicação, divisão) operando simultaneamente
- Computadores internos auxiliares para gerenciar jobs e E/S, liberando a CPU principal para cálculos
- Seymour Cray: figura lendária; continuou com 7600 e Cray-1

### Burroughs B5000 (1963)
- Projetado especificamente para **Algol 60** (precursora de C e Java)
- Primeiro computador com hardware otimizado para facilitar o trabalho do **compilador**
- Marco: reconheceu que **software também é importante** (ideia ignorada pelos concorrentes da época)

---

## Primeira Geração — Válvulas (1945–1955)

### COLOSSUS (1943, Reino Unido)
- Primeiro computador digital **eletrônico** do mundo
- Construído pelo governo britânico para decifrar mensagens do dispositivo **ENIGMA** alemão
- Alan Turing ajudou a projetar
- Mantido como segredo militar por ~30 anos → sem influência sobre máquinas subsequentes

### Mark I — Howard Aiken (Harvard, 1944)
- Aiken descobriu o trabalho de Babbage e decidiu construir o computador geral que Babbage não conseguiu, usando relés
- **72 palavras** de 23 algarismos decimais; tempo de instrução: **6 segundos**
- E/S: fita de papel perfurada
- Quando o sucessor Mark II ficou pronto, computadores de relés já eram obsoletos — a era eletrônica havia começado

### ENIAC (1946 — Eckert e Mauchley, Universidade da Pensilvânia)
- **18.000 válvulas** + 1.500 relés; **30 toneladas**; **140 kW** de consumo
- **20 registradores** de 10 dígitos decimais cada
- Programado com **6.000 interruptores** multiposição + floresta de cabos
- Concluído tarde demais para a WWII; o curso de verão de 1946 deflagrou uma explosão de interesse em computadores digitais

### EDSAC (1949 — Wilkes, Cambridge)
- Primeiro computador com **[[Programa Armazenado]]** efetivamente em operação
- Construído por Maurice Wilkes (o mesmo que inventaria a [[Microprogramação|microprogramação]] em 1951)

### Máquina IAS (1952 — Von Neumann, Princeton)

> A maioria dos computadores modernos usa esse projeto.

**Especificações:**
- **Memória:** 4.096 palavras de 40 bits (cada palavra = 2 instruções de 20 bits *ou* 1 inteiro de 40 bits)
- **Instrução:** 8 bits para tipo + 12 bits para endereço de memória
- **[[Registrador|Acumulador]]:** registrador interno de 40 bits dentro da [[ULA]]
- Sem aritmética de ponto flutuante (Von Neumann achava que o programador deveria controlar o ponto binário mentalmente)

**5 partes básicas** (mapeamento para Von Neumann):

| Componente IAS | Equivalente Von Neumann |
|---|---|
| Memória | [[Memória]] (M) |
| Unidade de lógica e aritmética | [[ULA]] (CA) |
| Unidade de controle | CC |
| ULA + Controle = **CPU** | [[Processador]] moderno |
| Entrada/Saída | [[Meio Externo de Gravação]] (R) |

### Whirlwind I (MIT, 1951)
- Palavra de **16 bits**; projetado para **controle em tempo real**
- Levou à invenção da **memória de núcleo magnético** por Jay Forrester
- Precursor dos minicomputadores

### IBM 701 (1953) / 704 (1956) / 709 (1958)
- **701:** primeiro computador IBM; 2.048 palavras de 36 bits; 2 instruções por palavra
- **704:** memória de núcleo magnético; inovação: **hardware de ponto flutuante**
- **709:** última máquina IBM de válvulas; basicamente um 704 expandido

---

## Geração Zero — Computadores Mecânicos (1642–1945)

### Blaise Pascal (1642)
- Primeira **calculadora operacional** da história — criada aos 19 anos para auxiliar o pai (coletor de impostos)
- Inteiramente mecânica (engrenagens + manivela)
- Limitação: apenas adição e subtração

### Gottfried Wilhelm von Leibniz (~1671)
- Máquina mecânica com as **quatro operações** (soma, subtração, multiplicação, divisão)
- Equivalente a uma calculadora de bolso três séculos antes do tempo

### Charles Babbage (1792–1871)
**Máquina diferencial:** calculava tabelas de navegação marítima; executava apenas um algoritmo fixo (diferenças finitas). Saída: perfurava resultados em chapas de cobre — precursor de cartões perfurados e CD-ROMs.

**Máquina analítica:** primeiro projeto de **computador de uso geral programável**. Estrutura:

| Componente (Babbage) | Equivalente moderno |
|---|---|
| Armazenagem | [[Memória]] — 1.000 palavras de 50 dígitos decimais |
| Moinho | [[ULA]] — soma, subtração, multiplicação, divisão |
| Seção de entrada | Leitora de cartões perfurados |
| Seção de saída | Saída perfurada e impressa |

> [!info] Ponte com Von Neumann
> A estrutura da Máquina Analítica é notavelmente similar à do [[Modelo de Von Neumann]] — projetado 100 anos depois. Babbage é considerado o "avô do computador digital moderno".

Programada em linguagem de montagem simples — exigia software. Para produzi-lo, Babbage contratou **Ada Augusta Lovelace** (filha do poeta Lord Byron): **primeira programadora da história**. A linguagem Ada foi batizada em sua homenagem.

**Limitação:** a precisão mecânica exigida (milhares de engrenagens) estava além da tecnologia do século XIX. O hardware nunca foi completamente depurado.

### Konrad Zuse (fim dos anos 1930)
- Estudante alemão que construiu calculadoras automáticas com **relés eletromagnéticos**
- Sem financiamento governamental; suas máquinas foram destruídas no bombardeio de Berlim (1944)
- Não conhecia o trabalho de Babbage; sem influência sobre máquinas subsequentes
- Considerado pioneiro da área

### John Atanasoff e George Stibbitz (EUA)
- **Atanasoff** (Iowa State): máquina com aritmética binária e memória de capacitores recarregados ("sacudir a memória") — precursor direto da **DRAM moderna**. Nunca se tornou operacional.
- **Stibbitz** (Bell Labs): calculadora de relés

---

## Ver também
- [[Modelo de Von Neumann]]
- [[EDVAC]]
- [[Programa Armazenado]]
- [[Microprogramação]]
- [[PC]]
- [[Níveis de Abstração]]
