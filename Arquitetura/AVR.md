---
title: AVR
aliases:
  - arquitetura AVR
  - Atmel AVR
  - megaAVR
  - tinyAVR
  - AVR XMEGA
tags:
  - computação/arquitetura
  - computação/histórico
date: 2026-04-06
---

# AVR

## Definição
[[ISA]] [[RISC e CISC|RISC]] de 8 bits voltada para [[Microcontrolador|microcontroladores]] de baixíssimo custo e sistemas embutidos. Presente no núcleo do [[Microcontrolador#Arduino|Arduino]]. Ver [[ISA#As três famílias de ISA mais relevantes]].

## Relações (SPO)
- AVR → criada por → Alf-Egil Bogen e Vegard Wollan (1996)
- AVR → adquirida e produzida por → Atmel
- AVR → usada no → [[Microcontrolador#Arduino|Arduino]] (megaAVR)
- AVR → organizada em → três classes (tinyAVR, megaAVR, XMEGA)
- AVR → possui → três tipos de memória (Flash, EEPROM, RAM)

## Origem histórica
- **1996** — Norwegian Institute of Technology: estudantes **Alf-Egil Bogen** e **Vegard Wollan** projetam uma CPU RISC de 8 bits
- Nome **AVR** = "**A**lf and **V**egard's **R**ISC processor"
- Após a conclusão do projeto, a **Atmel** o comprou e criou a **Atmel Norway**, onde os dois arquitetos continuaram refinando o processador
- **1997** — Atmel lança o primeiro microcontrolador AVR: **AT90S1200**
  - Pinagem idêntica ao **Intel 8051** (microcontrolador mais popular da época) → facilitar adoção

## Classes de microcontroladores AVR

| Classe | Flash | EEPROM | RAM | Pinos | Características |
|---|---|---|---|---|---|
| **tinyAVR** | 0,5–16 KB | 0–512 B | 32–512 B | 6–32 | Pequeno, E/S digital, entrada analógica; pinos de dupla função |
| **megaAVR** | 8–256 KB | 0,5–4 KB | 0,25–8 KB | 28–100 | Muitos periféricos, saída analógica, E/S serial, clocks internos |
| **AVR XMEGA** | 16–256 KB | 1–4 KB | 2–16 KB | 44–100 | Aceleração criptográfica, E/S USB |

> [!info] Filosofia de projeto
> A receita para o sucesso em microcontroladores: colocar **tudo** que o chip possivelmente precisará em um pacote **barato, pequeno e com poucos pinos**. Integração máxima = aplicabilidade máxima.

## Três tipos de memória no chip

| Tipo | Volatilidade | Conteúdo | Quem escreve |
|---|---|---|---|
| **Flash** | Não volátil | Código de programa e dados | Interface externa + altas voltagens |
| **EEPROM** | Não volátil | Configurações do usuário (ex: formato 12h/24h) | O próprio programa em execução |
| **RAM** | **Volátil** | Variáveis do programa em execução | O programa; perdida sem energia |

> [!warning] Distinção RAM vs. Flash/EEPROM
> Diferente da Flash e da EEPROM, a RAM perde todo o conteúdo ao desligar. Em sistemas embutidos, dados persistentes devem ser gravados na EEPROM, não na RAM.

## Periféricos do ATmega168 (megaAVR)
Exemplo concreto de integração: o **Atmel ATmega168** — o chip no coração de muitos Arduinos — inclui:

1. Três temporizadores (dois de 8 bits + um de 16 bits)
2. Clock de tempo real com oscilador
3. Seis canais PWM (modulação de largura de pulso) — controle de intensidade de luz, velocidade de motor
4. Oito canais de conversão analógico-digital (ADC) — leitura de tensões elétricas
5. Receptor/transmissor serial universal (UART)
6. Interface serial I²C — padrão para comunicação com sensores
7. Temporizador de vigia (watchdog) — detecta sistema travado
8. Comparador analógico no chip — compara duas tensões de entrada
9. Detector de falha de energia — interrompe o sistema quando a energia falta
10. Oscilador de clock interno programável — controla o clock da CPU

## ISA AVR — ATmega168

### Modelo de Memória — Espaços Separados

O ATmega168 tem **dois espaços de endereço independentes**:

| Espaço | Tamanho | Tipo | Uso |
|---|---|---|---|
| **Programa** | 16 KB (8K palavras de 16 bits) | Flash | Código executável |
| **Dados** | 1 KB | SRAM | Variáveis em execução |

O mesmo endereço numérico pode referenciar locais diferentes dependendo se o acesso é de instrução ou de dado. Isso permite usar Flash para programa (não-volátil) e SRAM para dados (rápida).

**Organização da memória de dados (espaço de dados):**

```
Endereço 0–31:   32 registradores de uso geral R0–R31
Endereço 32–95:  64 bytes de E/S (registradores de dispositivos internos)
Endereço 96–1119: SRAM (dados do programa)
```

### Arquivo de Registradores — R0 a R31

32 registradores de **8 bits** cada, chamados R0–R31.

**Aliasing com memória:** os registradores também existem no espaço de memória de dados:
- Byte 0 do espaço de dados = R0
- Byte 1 = R1
- ...
- Byte 31 = R31

Escrever em R5 via instrução de registrador e ler o byte de endereço 5 retorna o mesmo valor.

**Registradores com funções especiais:**

| Pares | Nome | Uso |
|---|---|---|
| R26/R27 | **X** | Ponteiro de 16 bits para endereçamento indireto |
| R28/R29 | **Y** | Ponteiro de 16 bits para endereçamento indireto |
| R30/R31 | **Z** | Ponteiro de 16 bits para endereçamento indireto + saltos indiretos |

Um registrador de 8 bits não é suficiente para endereçar 1 KB de SRAM (precisaria de 10 bits). Os pares X, Y, Z concatenam dois registradores de 8 bits para formar um endereço de **16 bits**, suficiente para até 64 KB.

### SREG — Registrador de Estado

8 bits, da esquerda para a direita:

| Bit | Símbolo | Nome | Descrição |
|---|---|---|---|
| 7 | **I** | Interrupt Enable | Habilita/desabilita todas as interrupções globalmente |
| 6 | **T** | Transfer | Armazenamento temporário de bit |
| 5 | **H** | Half Carry | Vai-um auxiliar (bit 3→4) — usado em BCD |
| 4 | **S** | Sign | XOR entre N e V |
| 3 | **V** | Overflow | Transbordo aritmético |
| 2 | **N** | Negative | Resultado negativo |
| 1 | **Z** | Zero | Resultado zero |
| 0 | **C** | Carry | Vai-um |

> [!info] Bit I — controle global de interrupções
> `I = 0`: todas as interrupções desabilitadas (uma instrução para silenciar tudo).
> `I = 1`: interrupções permitidas se o bit de habilitação do dispositivo também estiver setado.
> Cada dispositivo tem seu próprio bit de habilitação individual além do bit I global.

### Ponteiro de Pilha (SP)

Localizado na memória de E/S nos **endereços 80 (byte alto) e 81 (byte baixo)** — dois bytes consecutivos formando endereço de **16 bits**. Necessário porque 8 bits são insuficientes para endereçar 1 KB de SRAM.

### Segurança — Memória de Programa em Duas Seções

A Flash de 16 KB divide-se em:
- **Seção de carregador de inicialização (bootloader)**
- **Seção de aplicação**

Tamanho de cada seção configurado por **bits de fusível** (programados uma vez na fabricação).

**Regra de segurança:** somente código rodando na seção de bootloader pode **escrever na Flash**. Código da aplicação não pode modificar seu próprio código — nem o de outras aplicações.

Com **assinatura digital** adicional: o bootloader só carrega código assinado por fornecedor aprovado — base do modelo de segurança do TiVo e da Apple para seus dispositivos.

### Características do Conjunto de Instruções
- Modo único de operação (sem proteção de hardware — projetado para uma só tarefa)
- Único tipo de dado com suporte real: **byte de 8 bits**
- Ponteiros de 16 bits via pares X/Y/Z
- Instruções de **16 ou 32 bits** (6 formatos)
- Multiplicação com resultado em **R1:R0** (par de 16 bits)
- Desvios: relativo ao PC (12 bits), indireto via Z, ou endereço absoluto de 22 bits (instrução de 32 bits)

## Conexão com Arduino
O [[Microcontrolador#Arduino|Arduino]] usa o **megaAVR** (tipicamente ATmega168 ou ATmega328). A popularidade do Arduino tornou o AVR a plataforma RISC embutida mais conhecida entre iniciantes e makers. Ver [[Microcontrolador#Arduino]].

## Ver também
- [[ISA]]
- [[RISC e CISC]]
- [[Microcontrolador]]
- [[ARM]]
- [[Intel x86]]
- [[Memória]]
- [[Modos de Endereçamento]]
- [[Formatos de Instrução]]
- [[Tipos de Dados ISA]]
- [[Interrupção]]
