---
title: Sistema Operacional
aliases:
  - SO
  - OS
  - operating system
  - nível 3
tags:
  - computação/arquitetura
date: 2026-04-06
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Sistema Operacional

## Definição
Software que opera permanentemente no computador, interpretando as instruções do nível 3 da [[Níveis de Abstração|organização estruturada de computadores]] — aquelas que não existem no nível [[ISA]] (nível 2). Surgiu historicamente para automatizar o trabalho do operador humano de computadores.

## Relações (SPO)
- Sistema Operacional → ocupa → nível 3 dos [[Níveis de Abstração]]
- Sistema Operacional → interpreta → instruções adicionais não presentes no nível [[ISA]]
- Sistema Operacional → surgiu de → necessidade de automatizar operação manual (~1960)
- Sistema Operacional → expõe → [[Chamada do Sistema|chamadas do sistema]] ao programador
- Sistema Operacional → implementa → nível híbrido (parte executada diretamente, parte interpretada)

## Origem histórica

### A era pré-SO: programação manual
Nos primeiros computadores (décadas de 1940–1950), o programador operava a máquina pessoalmente:
1. Reservava horário numa planilha de utilização
2. Levava cartões perfurados à sala da máquina
3. Carregava manualmente o compilador (ex: FORTRAN), depois o programa, depois os dados
4. Esperava a execução — frequentemente interrompida por erros ou falhas de hardware
5. Se o programa travasse, consultava um **dump de memória** para depurar

> [!example] Processo típico com FORTRAN (anos 1950)
> 1. Pegar o maço de cartões do compilador FORTRAN
> 2. Ler o compilador na leitora de cartões
> 3. Ler o programa FORTRAN (às vezes em múltiplas passagens)
> 4. Compilar → gerar cartões em linguagem de máquina
> 5. Ler o programa em linguagem de máquina + biblioteca de sub-rotinas
> 6. Executar — e torcer para não travar

### Automação (~1960): o primeiro SO
Por volta de 1960, surgiu o conceito de **sistema operacional** para automatizar esse processo. Exemplo: **FMS (FORTRAN Monitor System)** no IBM 709.

O programador incluía **cartões de controle** junto ao programa:
- `*JOB` — identificação do serviço (contabilidade)
- `*FORTRAN` — instrução para carregar o compilador da fita
- `*DATA` — instrução para executar o programa traduzido com os dados fornecidos

Esses cartões de controle eram, na prática, as primeiras **instruções virtuais** de um novo nível de máquina.

## O Nível Híbrido

O nível 3 é **híbrido**: parte das suas instruções são idênticas às do nível [[ISA]] (executadas diretamente pelo microprograma) e parte são novas instruções adicionadas pelo SO (interpretadas pelo SO).

Com o tempo, essas novas instruções foram chamadas de:
- *macros de sistema operacional*
- *chamadas do supervisor*
- Hoje: **[[Chamada do Sistema]]** (system call)

## Evolução dos modelos de SO

| Modelo | Período | Característica |
|---|---|---|
| **Batch** (lote) | ~1960 | Programas executados em fila; horas de espera entre submissão e resultado |
| **Timesharing** (tempo compartilhado) | início 1960s | Múltiplos terminais remotos; programador obtém resultado quase imediato; CPU compartilhada |

> [!info] Timesharing — pioneiros
> Desenvolvido no Dartmouth College, MIT e outros no início dos anos 1960. Permitia que programadores digitassem programas e recebessem resultados em seus terminais — sem precisar ir à sala da máquina.

## Multiprogramação

Técnica introduzida com o IBM System/360 (1964): **vários programas residem na memória simultaneamente**. Enquanto um programa aguarda conclusão de operação de E/S, o [[Processador]] executa outro — aumentando a utilização da CPU.

> [!tip] Motivação
> Operações de E/S são muito mais lentas que o processador. Sem multiprogramação, a CPU fica ociosa esperando. Com ela, o tempo de espera de um programa vira tempo útil de outro.

## Emulação

Capacidade do SO (via [[Microprogramação|microprograma]]) de **simular outro computador**, executando programas binários de uma arquitetura diferente sem modificação. O IBM 360 emulava o 1401 e o 7094 via microprogramas específicos, permitindo migração de clientes sem reescrever software.

## O SO como camada de software (perspectiva CS:APP)

O SO é uma **camada interposta entre programas de aplicação e hardware**. Todo acesso ao hardware por parte de um programa de aplicação passa pelo SO.

### Duas finalidades fundamentais
1. **Proteção:** impedir que aplicações com defeito ou maliciosas danifiquem hardware ou outros programas
2. **Abstração:** fornecer às aplicações interfaces simples e uniformes para manipular hardware variado e complexo

### Três abstrações fundamentais

| Abstração | O que abstrai | Nota |
|---|---|---|
| **[[Processo]]** | Processador + Memória principal + E/S | Ilusão de uso exclusivo do hardware |
| **[[Memória Virtual]]** | Memória principal + disco | Ilusão de espaço de endereçamento privado |
| **Arquivo** | Dispositivos de E/S | Sequência uniforme de bytes |

### Arquivo — a abstração mais simples e poderosa
Um arquivo é uma **sequência de bytes** — nada mais, nada menos. Todo dispositivo de E/S é modelado como um arquivo:
- Disco → arquivo
- Teclado → arquivo
- Display → arquivo
- Rede → arquivo

Toda entrada/saída do sistema é realizada por leitura e escrita de arquivos via **Unix I/O** — um conjunto pequeno de [[Chamada do Sistema|chamadas de sistema]]. Essa uniformidade permite que o mesmo programa funcione com diferentes dispositivos sem modificação.

---

## Linux

Em agosto de 1991, Linus Torvalds — estudante de pós-graduação finlandês — anunciou modestamente um novo kernel Unix-like no grupo Usenet `comp.os.minix`:

> *"I'm doing a (free) operating system (just a hobby, won't be big and professional like gnu) for 386(486) AT clones."*

| Fato | Detalhe |
|---|---|
| **Ponto de partida** | **Minix** — SO desenvolvido por Andrew S. Tanenbaum para fins educacionais |
| **Ferramentas iniciais** | bash 1.08 e gcc 1.40 portados ainda em agosto de 1991 |
| **Combinação definitiva** | Kernel Linux + ferramentas [[Linguagem C#GNU — as ferramentas de C para o mundo livre|GNU]] = sistema Unix completo, compatível com POSIX |
| **Abrangência** | Disponível em dispositivos portáteis, PCs, mainframes — um grupo da IBM portou para um relógio de pulso |

> [!info] GNU + Linux
> O sistema que chamamos popularmente de "Linux" é, mais precisamente, **GNU/Linux**: o kernel Linux fornece o núcleo, e as ferramentas GNU (gcc, bash, binutils etc.) fornecem o ambiente de desenvolvimento e operação. Juntos, formam uma implementação POSIX completa do Unix.

---

## Origem do Unix

| Fato | Detalhe |
|---|---|
| **1969** | Ken Thompson, Dennis Ritchie, Doug McIlroy e Joe Ossanna (Bell Labs) iniciam projeto num PDP-7, escrito em linguagem de máquina — reação à complexidade do projeto Multics |
| **1970** | Brian Kernighan batiza o sistema "Unix" (trocadilho com "Multics") |
| **1973** | Kernel reescrito em C |
| **1974** | Unix anunciado publicamente |
| **Anos 1970–80** | Bell Labs distribui código-fonte para universidades → enorme adoção acadêmica |
| **Berkeley (BSD)** | Pesquisadores adicionam **memória virtual** e **protocolos de rede (TCP/IP)** → Unix 4.xBSD |
| **POSIX** | IEEE padroniza o Unix; Richard Stallman cunha o nome "POSIX" |
| **Standard Unix Spec.** | POSIX e Standard Unix Specification unificados → diferenças entre versões Unix desaparecem |

> [!info] Unix e C são inseparáveis
> O Unix foi o primeiro SO escrito quase inteiramente em C (após 1973). Isso permitiu portá-lo para novas arquiteturas com facilidade — expandindo simultaneamente a audiência do Unix e da linguagem C. Ver [[Linguagem C]].

---

## Escopo deste vault

O foco aqui é no papel do SO como **interpretador do nível 3** e como **camada de abstração** sobre o hardware. Os aspectos de gerenciamento de recursos, multitarefa e segurança estão detalhados em [[Processo]] e [[Memória Virtual]].

## Ver também
- [[Níveis de Abstração]]
- [[ISA]]
- [[Chamada do Sistema]]
- [[Microprogramação]]
- [[Tradução e Interpretação]]
- [[Processo]] — abstração do SO para programa em execução
- [[Memória Virtual]] — abstração do SO para memória
- [[Linguagem C]] — C foi criada para o Unix; GNU fornece as ferramentas
- [[Rede]] — rede como dispositivo de E/S gerenciado pelo SO
- [[Máquina Virtual]] — abstração do computador inteiro construída sobre o SO
