---
title: Coprocessador
aliases:
  - coprocessor
  - processador auxiliar
  - canal de E/S
  - processador de E/S
tags:
  - computação/arquitetura
date: 2026-04-21
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Coprocessador

## Definição

Segundo processador **especializado** acoplado à CPU principal. Executa tarefas específicas em paralelo com a CPU principal, liberando-a para continuar outro trabalho. Posição no espectro de acoplamento: mais fracamente acoplado que [[Multithreading no Chip|multithreading]] mas mais fortemente acoplado que [[Multiprocessadores|multiprocessadores]].

## Relações (SPO)
- Coprocessador → executa → tarefas especializadas em paralelo com a CPU principal
- Canal de E/S → é → coprocessador dedicado a entrada/saída
- FPU → é → coprocessador dedicado a aritmética de ponto flutuante
- GPU → é → coprocessador dedicado a processamento gráfico e SIMD massivo
- Coprocessador de rede → é → coprocessador dedicado a processamento de pacotes
- Coprocessador de criptografia → é → coprocessador para cifração simétrica e de chave pública

---

## Histórico

| Máquina | Coprocessador | Papel |
|---|---|---|
| IBM 360 e sucessores | Canais de E/S independentes | Controlam E/S sem intervenção da CPU principal |
| CDC 6600 | 10 processadores de E/S independentes | Operações de E/S massivamente paralelas |
| Intel x86 | 8087 / 80387 | Aritmética de ponto flutuante (FPU) |
| PCs modernos | GPU dedicada | Renderização 3D, shaders, GPGPU |

---

## Protocolo de Comunicação

Interação típica entre CPU principal e coprocessador:

```
CPU principal                  Coprocessador
     │                              │
     │── escreve descritor ─────────►│
     │   (comando + endereço         │
     │    dos dados em memória)      │
     │                       executa tarefa
     │◄── interrupção / polling ─────│
     │   (sinaliza conclusão)        │
```

1. CPU escreve **descritor de comando** em área de memória compartilhada ou registrador
2. Coprocessador lê, executa operação (E/S, cálculo, cifração)
3. Coprocessador sinaliza conclusão via **interrupção** ou a CPU faz **polling** de flag

---

## Tipos Comuns

### Canais de E/S
- Presente em mainframes IBM desde o 360
- Processador autônomo que lê **CCW** (Channel Command Words) da memória e transfere dados diretamente (DMA estendido)
- CPU emite `START SUBCHANNEL` e continua executando; canal sinaliza via interrupção ao concluir

### FPU — Floating Point Unit
- Originalmente chip separado (8087, 80387); hoje integrado ao chip da CPU
- Executa ADD, MUL, DIV, SQRT em FP de precisão simples/dupla/estendida
- Ainda funciona como "unidade funcional especializada" internamente

### GPU — Graphics Processing Unit
- Centenas a milhares de núcleos simples para processamento SIMD
- Programado via APIs como OpenGL, DirectX, CUDA, OpenCL
- Uso em GPGPU: simulação, aprendizado de máquina, criptografia

### Processador de Rede
- Analisa cabeçalhos de pacotes em hardware
- Implementa firewalls, QoS, NAT sem carga na CPU principal

### Processador de Criptografia
- Criptografia simétrica (AES) e assimétrica (RSA, ECC) são intensivas em computação
- Offload para coprocessador libera CPU para lógica da aplicação
- Exemplos: Intel QAT, chips HSM

---

## Ganho Típico

| Tipo de tarefa | Fator de ganho vs. CPU de uso geral |
|---|---|
| E/S com canal dedicado | Remove overhead total da CPU |
| FPU dedicada | 10–100× em cálculo de ponto flutuante |
| GPU para gráficos 3D | Ordens de magnitude |
| Criptografia em hardware | 5–50× dependendo do algoritmo |

---

## Ver também
- [[Concorrência e Paralelismo]] — espectro de acoplamento (chip → coprocessador → multiprocessador → multicomputador → grade)
- [[DMA]] — forma simples de coprocessamento para E/S
- [[Instruções SIMD]] — GPU como evolução de SIMD
- [[Multiprocessadores]] — próximo nível de paralelismo (memória compartilhada)
- [[FPU]] — unidade de ponto flutuante
