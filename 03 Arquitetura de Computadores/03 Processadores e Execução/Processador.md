---
title: Processador
aliases:
  - microprocessador
  - CPU
  - cérebro do computador
tags:
  - computação/fundamentos
date: 2026-04-06
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Processador

## Definição
Circuito integrado responsável pelo **processamento de dados** do computador. Sinônimos: **microprocessador**, **CPU** (Central Processing Unit), **UCP** (Unidade Central de Processamento). Na nomenclatura original do [[Modelo de Von Neumann]], corresponde ao conjunto formado pela **Central de Aritmética (CA)** e pela **Central de Controle (CC)**.

> [!warning] CPU ≠ computador
> Muitos usuários chamam o computador inteiro de "CPU". Isso é incorreto — CPU é o processador, não a máquina. Chamar o computador de CPU é tão equivocado quanto chamar um carro de "motor".

O processador precisa que o programa diga **exatamente** o que fazer — ele não é "inteligente" no sentido comum. Executa instruções explícitas, uma a uma.

### Processadores estão em todo lugar
Além dos PCs, processadores são encontrados em:
- Fornos de micro-ondas
- Lavadoras de roupa digitais
- Carros (injeção eletrônica, mesmo sem computador de bordo)
- Calculadoras, celulares, condicionadores de ar digitais, consoles de videogame

Para cada aplicação, normalmente é usado um **tipo diferente de processador** com um conjunto de instruções específico.

## Relações (SPO)
- Processador → é parte de → [[Hardware]]
- Processador → executa → [[Programa Armazenado]]
- Processador → contém → [[ULA]]
- Processador → contém → Central de Controle (CC)
- Processador → acessa → [[Memória]]
- Processador → corresponde a → CA + CC do [[Modelo de Von Neumann]]
- Processador → programado por → conjunto de instruções ([[ISA]])
- Instrução → é → sequência de bits com significado específico para a linha do processador

## Componentes internos (Von Neumann)

### Central de Aritmética (CA) — hoje: [[ULA]]
Unidade responsável pela execução de cálculos. Ver [[ULA]] para nomenclaturas modernas e variações.

### Central de Controle (CC)
Unidade responsável por **buscar instruções da [[Memória]]** e **decodificá-las** — convertendo cada instrução em sinais de controle que ativam ou desativam outras partes do processador. Em computadores modernos, está embutida dentro do próprio processador.

### Program Counter (PC) — Contador de Programa
[[Registrador]] especial que armazena o **endereço de memória da próxima instrução** a ser executada. Em qualquer instante, o PC aponta para uma instrução de máquina na memória principal. Após a decodificação de uma instrução, o PC é atualizado para indicar o endereço da próxima. Em instruções de desvio (`JMP`/`CALL`), o PC recebe o endereço de destino.

**Em x86**, o PC é implementado em dois registradores: **CS** (Code Segment — segmento de código) + **IP/EIP/RIP** (Instruction Pointer — ponteiro de instrução). Ver Capítulo 7 do livro de referência para detalhes.

> [!note] PC vs. "PC" (personal computer)
> "PC" como abreviação de *program counter* (registrador) é diferente de "PC" como *personal computer* — o contexto determina o significado.

## Ciclo de execução de instruções

O processador repete continuamente — do momento em que recebe energia até ser desligado:

```
1. Lê a instrução apontada pelo PC (fetch)
2. Interpreta os bits da instrução (decode)
3. Executa a operação indicada (execute)
4. Atualiza o PC para a próxima instrução
```

### As 4 operações básicas do processador

| Operação | Descrição |
|---|---|
| **Load** | Copia um byte ou palavra da [[Memória]] para um [[Registrador]], sobrescrevendo o conteúdo anterior |
| **Store** | Copia um byte ou palavra de um [[Registrador]] para um endereço de [[Memória]], sobrescrevendo o conteúdo anterior |
| **Operate** | Copia conteúdo de dois registradores para a [[ULA]], realiza operação aritmética/lógica e armazena o resultado em um registrador |
| **Jump** | Extrai um endereço da própria instrução e o copia para o PC, desviando o fluxo de execução |

> [!info] ISA vs. Microarquitetura
> O modelo de execução sequencial acima (uma instrução por vez, em ordem) é a **ISA** — o contrato público que o processador apresenta ao software. A **microarquitetura** é como o processador é *de fato* implementado internamente — com pipelines, execução fora de ordem, previsão de desvios — para executar muito mais rápido do que o modelo simples sugere. Ver [[ISA]] e [[Microprogramação]].

> [!info] Foco do estudo
> A maior parte do estudo de hardware de PCs é dedicada ao processador — ele é o componente que define o desempenho e as capacidades do [[Computador]].

> [!warning] Geração de calor
> O processador gera muito calor durante a operação. Um **cooler** (sistema de refrigeração) é indispensável — sem ele, o processador não funciona corretamente e pode queimar.

## Arquiteturas e evoluções

### Instruções e Conjunto de Instruções

Uma **instrução** é uma sequência de bits que possui um significado particular para o processador. O mesmo valor numérico pode ter significados completamente diferentes em linhas distintas. Por exemplo, `2Fh` pode significar "somar dois números" em uma linha e "ir para o endereço X" em outra.

O **conjunto de instruções** (ver [[ISA]]) é a tabela que relaciona cada instrução ao seu código numérico. Varia por linha; modelos mais novos da mesma linha podem ter instruções adicionais.

- Processadores x86 usam o conjunto de instruções **x86** (inteiros) e **x87** (ponto flutuante).
- **Compatibilidade dentro da linha:** processadores mais novos executam programas mais antigos; o inverso nem sempre vale se o programa usa instruções novas.

### x86 / IA32
Família de arquiteturas criada pela Intel, iniciada no 8088/8086. Evoluiu de 8 bits → 16 bits → **32 bits** (80386, 1985) → 64 bits. Chips compatíveis também fabricados pela **AMD** (maior fabricante x86-compatível). Dominam o mercado de PCs.

> [!note] "Intel" ≠ x86
> "x86" e "Intel" são frequentemente usados como sinônimos, mas x86 é a **linha** de processadores — não o fabricante. AMD também produz processadores x86-compatíveis. Para evitar ambiguidade, use "x86" para referir-se à arquitetura.

**Compatibilidade de software x86** depende também de:
1. O **sistema operacional** — um programa escrito para Windows não roda no Linux/macOS diretamente, mesmo sendo x86.
2. **Travas de hardware** — macOS verifica durante a instalação se está em um Mac; aborta em caso negativo.

### IA64 (Itanium)
Linha Intel para servidores, **incompatível** com x86 — não roda sistemas e programas x86 diretamente. Descontinuada em **2021**.

### RISC vs CISC
- **CISC** (ex: x86): muitas instruções complexas, microprogramadas
- **RISC**: poucas instruções simples, execução direta em hardware — mais rápido para cargas comuns
Ver [[RISC e CISC]].

### Arquitetura interna — Pipeline de estágios
Todo processador CISC possui pelo menos três unidades internas: **unidade de busca**, **unidade de decodificação** e **unidade de execução**. Essa estrutura é chamada de **pipeline de 3 estágios** (ou mais, em processadores modernos).

A técnica de **pipelining** permite que essas unidades trabalhem em paralelo em instruções diferentes, aumentando o throughput sem necessariamente aumentar o clock. Ver [[Pipeline]].

### Processador de X bits
O termo "processador de X bits" refere-se ao **tamanho dos registradores de uso geral (GPRs)**:

| Família | GPRs | "Processador de..." |
|---|---|---|
| 8088, 286 | 16 bits | 16 bits |
| 386, 486, Pentium… | 32 bits | 32 bits |
| Athlon 64, Core 2… | 64 bits | 64 bits |

> [!warning] "Instrução de X bits" é nomenclatura incorreta
> O tamanho do registrador **não tem relação com o comprimento da instrução** em CISC. Exemplos:
> - `mov eax, ebx` (registradores de **32 bits**) → opcode `89D8h` (**16 bits** de comprimento)
> - `mov ax, 1234h` (registrador de **16 bits**) → `B8 1234h` (**24 bits** de comprimento)
> - `mov eax, 12345678h` (registrador de **32 bits**) → `B8 12345678h` (**40 bits** de comprimento)
>
> Instruções CISC têm comprimento variável — ver [[RISC e CISC#Comprimento Variável das Instruções (CISC)]].

Além disso, x86 tem dois modos de operação — "modo protegido de 32 bits" e "modo de 64 bits" — o que origina "sistemas operacionais de 32 bits" e "de 64 bits".

### Clock e Desempenho
O clock do processador tem dois papéis internos:
1. **Sincronismo:** comanda o momento em que dados são transferidos de uma unidade para outra no pipeline
2. **Previsibilidade:** a unidade de decodificação sabe exatamente quantos ciclos cada instrução precisa, coordenando o envio à unidade de execução sem desperdício

> [!warning] Clock ≠ desempenho entre modelos diferentes
> Só é válido comparar clocks entre processadores da **mesma microarquitetura**. Processadores com microarquiteturas diferentes podem ter eficiências radicalmente distintas por instrução.
>
> Exemplo: se a instrução X demora 30 ciclos no processador A e 15 ciclos no processador B, o processador B com **metade do clock** de A terá o mesmo desempenho para essa instrução.

**IPS e FLOPS** (Instructions/Floating-point Operations Per Second) são métricas problemáticas em CISC:
- Instruções CISC não são padronizadas — o resultado varia conforme o mix de instruções usado na medição
- Cálculos teóricos assumem o processador no pico o tempo todo (irrealista)
- O desempenho percebido depende também de RAM, armazenamento (SSD vs. HD) e GPU

Ver [[RISC e CISC#Tempo de Execução e Ciclos de Máquina]] para mais contexto.

### Superescalar
CPUs que executam **múltiplas instruções simultaneamente**, às vezes fora de ordem. Surgiu nos anos 1990 como forma de aumentar desempenho além dos limites do clock.

### Multicore
Múltiplos núcleos de processamento no mesmo chip. IBM POWER4 (2001): primeiro processador dual-core. Desafio: exige programação explicitamente paralela.

Cada núcleo possui suas próprias caches L1 e L2; a cache L3 é **compartilhada** entre todos os núcleos. Ver [[Concorrência e Paralelismo#Multi-core]].

### Hyperthreading (simultaneous multi-threading)
Técnica que permite a um único núcleo executar **múltiplos fluxos de controle**. Mantém múltiplas cópias de partes do hardware (PC, registradores) mas apenas uma cópia de outras (unidade de ponto flutuante). Enquanto um thread aguarda um dado vir da cache, o núcleo pode avançar com outro thread — aproveitando melhor os recursos.

**Exemplo:** Intel Core i7 executa 2 threads por núcleo → 4 núcleos = 8 threads em paralelo.

### Paralelismo a nível de instrução (ILP)
Processadores modernos executam **múltiplas instruções ao mesmo tempo**:

| Era | Taxa típica |
|---|---|
| Intel 8086 (1978) | 3–10 ciclos por instrução |
| Processadores modernos | 2–4 instruções por ciclo |
| Técnicas avançadas | Até 100 instruções em voo |

O **pipeline** é a técnica central: as etapas de execução de uma instrução (fetch, decode, execute, write-back) são distribuídas em estágios paralelos que processam instruções diferentes ao mesmo tempo. Ver [[Concorrência e Paralelismo#Paralelismo a nível de instrução]].

### SIMD
Hardware especial que permite a uma **única instrução** operar sobre múltiplos dados em paralelo. Exemplo: somar 8 pares de floats em uma instrução. Muito usado em processamento de imagem, áudio e vídeo. Ver [[Concorrência e Paralelismo#SIMD — Instrução única, múltiplos dados]].

## Ver também
- [[ULA]]
- [[Hardware]]
- [[Computador]]
- [[Modelo de Von Neumann]]
- [[Programa Armazenado]]
- [[Chipset]]
- [[Placa-mãe]]
- [[RISC e CISC]]
- [[Firmware]] — o que o processador executa ao ser ligado (UEFI/BIOS/POST)
- [[Pipeline]] — unidades de busca/decodificação/execução; pipelining; stalls
- [[Flag]] — registradores de status (overflow, sign, zero, carry, parity)
- [[Interrupção]] — pedidos de hardware/software para suspender execução
- [[Concorrência e Paralelismo]] — multicore, hyperthreading, ILP, SIMD
- [[Lei de Amdahl]] — limites teóricos dos ganhos de desempenho
