---
title: Microcontrolador
aliases:
  - computador embutido
  - embedded computer
  - sistema embutido
tags:
  - computação/fundamentos
date: 2026-04-06
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Microcontrolador

## Definição
[[Computador]] completo em um único chip (processador + memória + E/S), embutido em dispositivos que não são vendidos como computadores. Gerencia o dispositivo e sua interface com o usuário.

## Relações (SPO)
- Microcontrolador → é um tipo de → [[Computador]]
- Microcontrolador → contém → [[Processador]], [[Memória]], circuitos de E/S
- Microcontrolador → opera em → tempo real
- Microcontrolador → presente em → praticamente todo dispositivo elétrico moderno

## Categorias de dispositivos que usam microcontroladores

| Categoria | Exemplos |
|---|---|
| Eletrodomésticos | Rádio-relógio, máquina de lavar, forno de micro-ondas, alarme |
| Comunicação | Telefone celular, fax, pager |
| Periféricos de PC | Impressora, scanner, modem, drive óptico |
| Entretenimento | DVD player, MP3 player, TV, câmera digital |
| Médico | Raio-X, RMI, monitor cardíaco, termômetro digital |
| Militar | Mísseis guiados, torpedos |
| Vendas | ATM (caixa eletrônico), caixa registradora |
| Brinquedos e jogos | Bonecas que falam, consoles, radiocontrole |

> [!info] Escala
> Um carro de alta qualidade pode ter **50+ microcontroladores**; um avião a jato, **200+**. Uma família pode possuir centenas de computadores sem saber. Em número de unidades vendidas por ano, microcontroladores superam todas as outras categorias de computadores por ordens de grandeza.

## Características distintivas

### 1. Custo extremamente sensível
Empresas que compram milhões de unidades baseiam a escolha em diferenças de **1 centavo** por unidade. Um microcontrolador de 8 bits em grande volume pode custar **$0,10**. Esse preço é o que permite colocar um computador em um relógio de $10.

### 2. Operação em tempo real
Recebem um estímulo e devem responder **instantaneamente** — apertar um botão acende uma luz sem atraso perceptível. Essa exigência impacta diretamente a arquitetura.

### 3. Restrições físicas
Tamanho, peso, consumo de bateria e limitações elétricas/mecânicas são restrições de projeto de primeira ordem (ao contrário de PCs).

### 4. Software embutido em ROM
Na maioria dos casos, o software está gravado diretamente no chip como [[Memória]] somente de leitura (ROM), criada na fabricação.

## Tipos

| Tipo | Característica |
|---|---|
| Propósito geral | Computador pequeno e comum; versões de 4, 8, 16, 32 bits |
| Propósito específico | ISA e arquitetura otimizadas para aplicação (ex: multimídia) |

## Arduino
Plataforma de microcontrolador de hardware aberto, criada por **Massimo Banzi e David Cuartielles** (Ivrea, Itália). Baseada no microprocessador **RISC de 8 bits Atmel [[AVR]]** (especificamente o **megaAVR**, como o ATmega168/ATmega328). Custo: <$20. Linguagem de programação: **Wiring** (projetada para controle em tempo real). Grande comunidade e ecossistema de projetos.

Ver [[AVR]] para a história completa da arquitetura, classes e periféricos do ATmega168.

> [!tip] Por que Arduino é relevante
> Demonstra a democratização do hardware: qualquer pessoa pode montar e programar sistemas computacionais embarcados sem acesso a fábrica de chips.

## Ver também
- [[Tipos de Computadores]]
- [[Processador]]
- [[Memória]]
- [[RISC e CISC]]
- [[Computador]]
