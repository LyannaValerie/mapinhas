---
title: Sistema de Memória Vetorial
tags:
  - hardware
  - arquitetura
  - DLP
  - vetor
  - memória
aliases:
  - vector memory system
  - bancos de memória vetorial
  - bank conflicts
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Sistema de Memória Vetorial

Para atingir 1 elemento/ciclo, o sistema de memória precisa de múltiplos bancos independentes. Aprofundamento em [[Arquitetura Vetorial#Bancos de Memória|bancos de memória vetorial]] com modelo de timing completo.

Fonte: P&H "Computer Architecture: A Quantitative Approach", Apêndice G.3.

## Timing de banco de memória

| Parâmetro | Definição |
|---|---|
| **Latência de acesso** | Tempo do endereço chegar ao banco até dado retornar ao CPU |
| **Tempo de ciclo / busy time** | Tempo que o banco fica ocupado — governa largura de banda efetiva |

| Tipo | Latência vs. busy time |
|---|---|
| SRAM sem pipeline | latência ≈ busy time |
| SRAM pipelined | latência > busy time (elemento ocupa 1 estágio) |
| DRAM | latência < busy time (precisa de tempo extra para restaurar dado após leitura destrutiva) |

A latência de acesso determina o **start-up time** de instruções de load vetorial (inclui travessia da rede de interconexão).

O busy time determina a **largura de banda sustentada** — um segundo acesso ao mesmo banco deve esperar o busy time expirar.

## Quantos bancos são necessários?

Para sustentar 1 elemento/ciclo sem stalls com acesso de $T$ ciclos:

$$\text{bancos mínimos} = T$$

Arredondar para potência de 2 na prática.

**Exemplo**: acesso de 6 ciclos → mínimo 6 bancos → escolher **8 bancos**.

### Timing com 8 bancos, latência 6 ciclos (vetor de 64, início no byte 136)

CPU emite 1 endereço/ciclo, recebe 1 dado/ciclo após latência inicial:

- Ciclo 0: banco 0 recebe endereço 136 → ocupa por 6 ciclos
- Ciclo 1: banco 1 recebe endereço 144
- Ciclo 2: banco 2 recebe endereço 152
- ...
- Ciclo 6: banco 0 retorna dado; banco 0 recebe novo endereço 200
- Ciclo 7: banco 1 retorna dado; recebe 208

Taxa sustentada: 1 elemento/ciclo após start-up de 6 ciclos.

## Conflitos de banco

Condição de ausência de conflito dentro de uma instrução vetorial:

$$\gcd(\text{stride}, \text{n\_bancos}) = 1$$

Exemplos com stride e n_bancos variando:

| n_bancos | stride | Frequência de conflito |
|---|---|---|
| 16 | 16 | todo acesso — serialização total |
| 16 | 8 | a cada 2 acessos |
| 64 | 8 | a cada 8 acessos |
| 64 | 32 | a cada 2 acessos |
| qualquer | 1 | nenhum (unit-stride) |

Quando não há conflito: **unit stride = non-unit stride em taxa de transferência**.

> [!tip] Estratégia prática
> Aumentar número de bancos reduz frequência de conflito para strides não-unitários.
> Em 2011, supercomputadores vetoriais usam centenas de bancos por CPU.
> Programadores favorecem unit-stride sempre que possível.

## Conflitos inter-instrução

Mesmo com unit stride, conflitos podem ocorrer entre os **últimos elementos de uma instrução e os primeiros da próxima**:

- Aumentar número de bancos reduz probabilidade desses conflitos

## Múltiplos processadores / múltiplos pipelines

Com múltiplos CPUs ou pipelines de memória compartilhando o mesmo sistema:
- Mais bancos necessários para evitar conflitos cruzados
- Redes de chaveamento multi-estágio conectam pipelines aos bancos
- Congestionamento na rede causa stalls adicionais

## Scatter / Gather e conflitos

Acesso indexado (scatter/gather) é mais suscetível a conflitos de banco:
- Cada elemento tem endereço independente → sem padrão previsível
- Mais lento que acesso strided, mas ainda muito mais rápido que loop escalar

Ver [[Arquitetura Vetorial#Scatter / Gather (acesso indexado)|Scatter/Gather]] para código e semântica.

## Relações (SPO)

- Latência de acesso → determina → start-up time de load vetorial
- Busy time de banco → governa → largura de banda efetiva
- $\gcd(\text{stride}, \text{n\_bancos}) = 1$ → garante → ausência de conflito intra-instrução
- Mais bancos → reduz → frequência de conflito para strides não-unitários
- DRAM → tem → busy time > latência de acesso
- Rede de chaveamento multi-estágio → conecta → pipelines de memória aos bancos

## Ver também

- [[Arquitetura Vetorial]] — stride, scatter/gather, bancos de memória
- [[Desempenho de Processadores Vetoriais]] — start-up time de load: 12 ciclos no VMIPS
- [[Hierarquia de Memória]] — latência, largura de banda, DRAM vs. SRAM
- [[Cray X1]] — 2048 requests in-flight por MSP; linhas de 32 bytes para minimizar desperdício
