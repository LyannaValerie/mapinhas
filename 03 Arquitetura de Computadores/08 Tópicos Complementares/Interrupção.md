---
title: Interrupção
aliases:
  - interrupções
  - interrupt
  - IRQ
  - INTR
  - NMI
  - Non-Maskable Interrupt
  - IPI
  - InterProcessor Interrupt
  - APIC
  - Advanced Programmable Interrupt Controller
  - interrupção de hardware
  - interrupção de software
  - exceção
  - exceções
  - exception
  - IDT
  - Interrupt Descriptor Table
  - handler
  - General Protection Fault
  - page fault
  - divide by zero
tags:
  - computação/arquitetura
date: 2026-04-09
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Interrupção

## Definição
Sinal enviado ao [[Processador]] para que ele **suspenda temporariamente** a execução do programa corrente e atenda a quem gerou o pedido. Após o atendimento, o processador retoma o programa no ponto exato onde parou.

Interrupções e **exceções** funcionam pelo mesmo mecanismo — diferem apenas na origem.

## Relações (SPO)
- Interrupção → suspende → execução do programa atual
- Interrupção → gerada por → hardware, software ou outro processador
- INTR → aceita → interrupções mascaráveis (IF=1 em EFLAGS)
- NMI → aceita → interrupções não-mascaráveis
- APIC → expande → linhas de interrupção mascarável para múltiplos dispositivos
- IDT → armazena → endereços dos handlers de cada interrupção/exceção
- Processador → retoma → programa após atendimento

---

## Os 4 Tipos de Interrupção

### 1. Hardware Mascarável
Via pino **INTR**. Controlada pela flag **IF** (Interrupt enable Flag) do EFLAGS:
- **CLI** (CLear Interrupt enable): desabilita — coloca IF em 0
- **STI** (SeT Interrupt enable): habilita — coloca IF em 1

Processadores x86 têm **apenas um pino INTR**. Para conectar múltiplos periféricos, os PCs usam um **APIC** (Advanced Programmable Interrupt Controller) externo, hoje embutido no chipset da placa-mãe.

> [!example] Teclado e mouse
> Ao pressionar uma tecla, o controlador do teclado gera uma interrupção mascarável. O processador suspende a execução atual por frações de segundo, lê o valor da tecla e retoma o programa.

### 2. Hardware Não-Mascarável
Via pino **NMI**. Não pode ser ignorada pelo software. Usada para falhas críticas: erro de RAM, falha no armazenamento.

### 3. Inter-Processor Interrupt (IPI)
Em sistemas **multiprocessados** (múltiplos processadores ou múltiplos núcleos), um processador/núcleo usa IPI para **comunicar-se com outro**.

### 4. Software
Gerada por programas via instrução **INT**. Mecanismo base das **chamadas de sistema** (syscalls). Ver [[Chamada do Sistema]].

---

## APIC — Advanced Programmable Interrupt Controller

Controlador interno do processador responsável por gerenciar todas as interrupções. Em PCs modernos, o APIC está embutido no [[Chipset]] da [[Placa-mãe]] e expande a única linha INTR para múltiplas linhas de dispositivos.

---

## Exceções vs. Interrupções

| | Exceção | Interrupção |
|---|---|---|
| **Origem** | O próprio programa em execução | Dispositivo externo (E/S) ou outro evento assíncrono |
| **Sincronismo** | **Síncrona** — ocorre no mesmo ponto toda vez que o programa roda com os mesmos dados | **Assíncrona** — pode variar dependendo de quando o dispositivo termina |
| **Exemplo** | Divisão por zero, opcode inválido, page fault | Teclado, disco, timer |
| **Reprodutibilidade** | 100% reproduzível | Não reproduzível (depende de timing externo) |

---

## Sequência de Tratamento de Interrupção

Quando um dispositivo de E/S conclui uma operação e gera uma interrupção:

### Ações do Hardware
1. Controlador de dispositivo ativa linha de interrupção no barramento
2. CPU, quando pronta, ativa sinal de **reconhecimento de interrupção** (ACK)
3. Controlador de dispositivo coloca o **vetor de interrupção** (número inteiro) nas linhas de dados
4. CPU salva o vetor temporariamente
5. CPU empilha **PC + PSW** (contador de programa e palavra de estado) na [[Pilha]]
6. CPU usa o vetor como índice na tabela de interrupções (IDT / tabela de vetores) para carregar o novo PC; PSW pode ser modificado (ex: desabilitar interrupções)

### Ações do Software (ISR — Interrupt Service Routine)
7. ISR salva todos os registradores que vai usar (na pilha ou em tabela do sistema)
8. Identifica qual dispositivo específico gerou a interrupção (vetor pode ser compartilhado entre dispositivos do mesmo tipo)
9. Lê informações adicionais (código de estado, dados disponíveis etc.)
10. Trata erros de E/S se houver
11. Atualiza variáveis globais de controle (ponteiro e contador do buffer); envia próximo byte para o buffer do dispositivo se houver mais a enviar
12. Envia código de fim de interrupção ao controlador (EOI — End of Interrupt), se necessário
13. Restaura todos os registradores salvos
14. Executa **IRET** (Return from Interrupt) — restaura PC e PSW, retoma o programa exatamente no estado anterior

> [!important] Transparência
> Quando todas as etapas acima são seguidas corretamente, a interrupção é **transparente**: o programa interrompido retoma sem saber que foi suspenso. A transparência é a propriedade fundamental que torna interrupções seguras. Uma ISR que não salva/restaura todos os registradores viola a transparência.

---

## Prioridades de Interrupção

Quando múltiplos dispositivos competem por atenção simultânea:

- Cada dispositivo tem uma **prioridade** (número inteiro)
- A CPU também opera com uma prioridade, controlada por campo no PSW
- Uma ISR de prioridade *n* só pode ser interrompida por dispositivo de prioridade **maior que n**
- Dispositivos de prioridade menor ficam **pendentes** até que a ISR atual conclua

### Exemplo — sequência temporal com 3 dispositivos

| Dispositivo | Prioridade |
|---|---|
| Impressora | 2 |
| Disco | 4 |
| RS232 | 5 |

```
t=0:  programa do usuário rodando
t=10: impressora interrompe (prio 2) → ISR impressora inicia
t=15: RS232 interrompe (prio 5 > 2) → ISR RS232 preempeta ISR impressora
t=20: disco interrompe (prio 4 < 5 atual) → mantido pendente
t=25: ISR RS232 termina → retorna para ISR impressora (prio 2)
      → imediatamente: disco (prio 4 > 2) interrompe → ISR disco executa
t=35: ISR disco termina → retorna para ISR impressora
t=40: ISR impressora termina → retorna ao programa do usuário
```

> [!tip] Dois níveis no x86 (mascarável / não-mascarável)
> O x86 original tem apenas dois níveis de prioridade de hardware. Para múltiplas prioridades, usa-se o **controlador 8259A** (ou APIC moderno) externo. O 8259A retém interrupções de prioridade inferior e as libera somente quando a ISR atual termina e a CPU envia o sinal EOI.

---

## Exceções

Exceções são **mensagens de erro internas** do processador — geradas quando o próprio processador detecta uma situação especial durante a execução.

Exceções fazem parte do uso **normal** do sistema. Exemplos:
- Uma **page fault** (#PF) é gerada toda vez que uma página está no arquivo de troca — o SO a carrega para a RAM e o programa continua
- **Debug** (#DB) é gerada quando o programa atinge um breakpoint definido pelo programador

Cada exceção tem um **número de vetor** (0–31) e um mnemônico. Vetores 32–255 são reservados para interrupções mascaráveis.

### Mecanismo de Evento e Handler

Quando ocorre um **evento** durante a execução de Icurr:

1. Processador detecta o evento
2. Usa o número do evento como índice na **tabela de exceções** — *jump table* onde a entrada k contém o endereço do handler da exceção k
3. Faz chamada indireta ao **exception handler** correspondente
4. Handler conclui → um de três resultados:
   - Retorna para **Icurr** — instrução que estava executando quando o evento ocorreu
   - Retorna para **Inext** — próxima instrução que executaria normalmente
   - **Aborta** o programa interrompido

O evento pode ser:
- **Relacionado** à instrução corrente: page fault, overflow aritmético, divisão por zero
- **Não relacionado** à instrução: timer do sistema, conclusão de operação de E/S

> [!note] ECF em nível hardware
> Esse mecanismo é a forma de [[Fluxo de Controle Excepcional|ECF]] em nível hardware — base para E/S, processos e memória virtual.

### Tabela de Exceções x86

| Vetor | Mnemônico | Nome | Situação |
|---|---|---|---|
| 0 | #DE | Divisão por zero | Instrução de divisão com divisor zero |
| 1 | #DB | Debug | PC atingiu um breakpoint definido |
| 2 | — | NMI | Interrupção não-mascarável |
| 3 | #BP | Breakpoint | Instrução de parada para debug |
| 4 | #OF | Overflow | Instrução INTO com overflow detectado |
| 5 | #BR | Valor fora dos limites | Instrução BOUND com valor fora do intervalo |
| 6 | #UD | Opcode inválido | Valor não corresponde a nenhuma instrução |
| 7 | #NM | Dispositivo não disponível | Instrução matemática/SIMD sem FPU ou sem suporte |
| 8 | #DF | Falha dupla | Erro ao tratar uma exceção anterior |
| 9 | #MF | (Reservado) | — |
| 10 | #TS | TSS inválido | Tabela TSS de uma tarefa é inválida |
| 11 | #NP | Segmento não presente | Registrador de segmento aponta para segmento inválido |
| 12 | #SS | Exceção de pilha | Stack overflow/underflow ou SS inválido |
| 13 | #GP | **Falha geral de proteção** | Programa tenta invadir área de outro (GPF) |
| 14 | #PF | **Falha de página** | Página no swap ou permissão negada |
| 15 | — | (Reservado) | — |
| 16 | #MF | Exceção da FPU | Divisão por zero, operação inválida na [[FPU]] |
| 17 | #AC | Verificação de alinhamento | Acesso a endereço desalinhado com verificação ativa |
| 18 | #MC | Verificação de máquina | Depende do modelo; habilitada por MCE em CR4 |
| 19 | #XM/#XF | Exceção SIMD | Erro em instrução SIMD (Intel: #XM; AMD: #XF) |
| 20 | #VE | Exceção de virtualização | Erro no sistema de virtualização (Intel apenas) |
| 21 | #CP | Proteção de controle | Tecnologia CET (Control-flow Enforcement) |
| 22–28 | — | (Reservados) | — |
| 29 | #VC | Comunicação VMM | Virtualização AMD apenas |
| 30 | #SX | Exceção de segurança | Virtualização AMD — dados sensíveis |
| 31 | — | (Reservado) | — |
| 32–255 | — | Interrupções mascaráveis | Hardware e software |

---

## Funcionamento: IDT

A **IDT** (Interrupt Descriptor Table — Tabela de Descrição de Interrupções) armazena as localizações e propriedades dos segmentos de memória contendo os **handlers** (código que trata cada interrupção/exceção).

O registrador **IDTR** ([[Registradores x86#Registradores de Gerenciamento de Memória|Interrupt Descriptor Table Register]]) armazena o endereço linear inicial da IDT.

Ver [[Proteção de Memória x86]] para a estrutura da IDT como tabela de descritores.

---

## Ver também
- [[Fluxo de Controle Excepcional]] — exceções como ECF em nível hardware
- [[Processador]]
- [[Registradores x86]] — IF flag em EFLAGS; IDTR
- [[Sistema Operacional]]
- [[Chamada do Sistema]] — interrupções de software como mecanismo de syscall
- [[Proteção de Memória x86]] — IDT como tabela de descritores
- [[Paginação x86]] — page fault (#PF) como exceção normal do sistema de paginação
- [[Multitarefa]] — temporizador gera interrupção para forçar troca de tarefa
- [[Chipset]] — APIC embutido no chipset moderno
- [[Hardware]]
- [[DMA]] — controlador DMA gera interrupção ao concluir transferência
- [[Pilha]] — PC e PSW são empilhados na entrada da ISR
