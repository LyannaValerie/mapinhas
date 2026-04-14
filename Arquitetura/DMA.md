---
title: DMA
aliases:
  - Direct Memory Access
  - acesso direto à memória
tags:
  - computação/arquitetura
date: 2026-04-06
---

# DMA — Direct Memory Access

## Definição
Método de transferência de dados que opera **diretamente entre o [[Meio Externo de Gravação]] (R) e a [[Memória]] (M)**, sem envolver o [[Processador]]. Corresponde ao caminho "preferível" da arquitetura de [[Modelo de Von Neumann]].

## Relações (SPO)
- DMA → transfere dados entre → [[Meio Externo de Gravação]] e [[Memória]]
- DMA → libera → [[Processador]] durante transferência
- DMA → contrasta com → acesso via [[Processador]] (caminho "possível")
- DMA → componente de → [[Modelo de Von Neumann]]

## Comparação dos Caminhos de E/S

| | Via Processador ("possível") | DMA ("preferível") |
|---|---|---|
| Implementação | Simples | Complexa |
| Processador durante transferência | Ocupado | Livre para outras tarefas |
| Eficiência | Baixa | Alta |

> [!tip] Por que é mais eficiente?
> No DMA, o [[Processador]] não participa da transferência de dados. Isso permite que ele continue executando outras instruções em paralelo enquanto os dados trafegam entre [[Meio Externo de Gravação]] e [[Memória]].

> [!note] Complexidade do DMA
> Uma das dificuldades de implementação é garantir que o [[Processador]] não tente acessar a mesma área de [[Memória]] que está sendo usada pela transferência DMA simultaneamente — o que exige mecanismos de controle de acesso à memória.

---

## Três Esquemas de E/S

### 1. E/S Programada (Espera Ocupada — Busy Wait)

CPU executa um laço apertado verificando repetidamente o registrador de estado do dispositivo até ele estar pronto.

```java
// Pseudocódigo
for (i = 0; i < count; i++) {
    do {
        status = in(display_status_reg);
        ready = (status >> 7) & 0x01;
    } while (ready != 1);       // laço de espera
    out(display_buffer_reg, buf[i]);
}
```

- **Vantagem:** simples de implementar
- **Desvantagem:** CPU desperdiçada 100% durante a espera — inaceitável quando há outro trabalho a fazer
- **Uso atual:** microcontroladores simples (ex: [[AVR]]), sistemas de tempo real sem SO

### 2. E/S por Interrupção

CPU inicia o dispositivo, habilita interrupções e continua executando outro código. Quando o dispositivo conclui, gera uma [[Interrupção]].

- CPU processa a [[Interrupção#Sequência de Tratamento de Interrupção|ISR]] para cada byte/palavra transferida
- **Problema:** overhead de interrupção por byte é alto para dispositivos rápidos (discos, redes)
- **Vantagem:** CPU fica livre entre transferências individuais

### 3. DMA — Acesso Direto à Memória

Contrata um chip dedicado (controlador DMA) para executar a transferência inteira sem envolver a CPU.

#### Registradores do Controlador DMA

| Registrador | Conteúdo |
|---|---|
| **Endereço** | Endereço de memória inicial para leitura/escrita |
| **Contagem** | Número de bytes (ou palavras) a transferir |
| **Dispositivo** | Número do dispositivo de E/S de origem/destino |
| **Direção** | READ (dispositivo → memória) ou WRITE (memória → dispositivo) |

#### Operação

1. CPU escreve os 4 registradores acima e libera o controlador DMA
2. DMA faz requisições de barramento (leitura e escrita) independentemente da CPU
3. A cada byte transferido: incrementa endereço, decrementa contagem
4. Quando contagem = 0: DMA para e gera **uma única interrupção** à CPU

> [!example] Exemplo: escrever 32 bytes a partir do endereço 100 para o dispositivo 4
> CPU escreve: endereço=100, contagem=32, dispositivo=4, direção=WRITE.
> DMA faz 32 leituras de memória e 32 escritas no dispositivo.
> CPU recebe 1 interrupção ao final — em vez de 32.

#### Roubo de Ciclo (Cycle Stealing)

O controlador DMA compete com a CPU pelo barramento. Quando o DMA requisita um ciclo de barramento:
- CPU deve **esperar** (DMA tem prioridade maior — dispositivos de E/S não toleram atraso)
- Esse processo = **roubo de ciclo**: a CPU perde ciclos de clock para o DMA

> [!tip] Trade-off
> Apesar do roubo de ciclo, o ganho de não processar uma interrupção por byte transferido compensa com ampla margem para dispositivos de alta largura de banda (discos, rede).

---

## Comparação dos Três Métodos

| Método | CPU durante transferência | Overhead | Uso ideal |
|---|---|---|---|
| **E/S Programada** | Ocupada (busy wait) | Nenhum hardware extra | Microcontroladores simples, sistemas em tempo real |
| **E/S por Interrupção** | Livre entre bytes | 1 interrupção por unidade | Dispositivos lentos (teclado, serial) |
| **DMA** | Livre (exceto roubo de ciclo) | 1 interrupção por bloco | Discos, redes, dispositivos de alta velocidade |

## Ver também
- [[Memória]]
- [[Meio Externo de Gravação]]
- [[Processador]]
- [[Modelo de Von Neumann]]
- [[Interrupção]] — DMA gera interrupção ao concluir transferência
- [[Barramento]] — DMA acessa o barramento diretamente (roubo de ciclo)
- [[AVR]] — E/S programada comum em microcontroladores
