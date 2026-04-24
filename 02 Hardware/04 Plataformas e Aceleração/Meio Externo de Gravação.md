---
title: Meio Externo de Gravação
aliases:
  - armazenamento externo
  - R
  - storage
  - dispositivos de entrada
  - dispositivos de saída
tags:
  - computação/fundamentos
  - computação/arquitetura
date: 2026-04-06
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Hardware]]

# Meio Externo de Gravação

## Definição
Componente do [[Modelo de Von Neumann]] que engloba **tudo que está localizado fora do [[Processador]]**: sistemas de armazenamento, dispositivos de entrada e dispositivos de saída.

## Relações (SPO)
- Meio Externo de Gravação → localizado fora de → [[Processador]]
- Meio Externo de Gravação → componente de → [[Modelo de Von Neumann]]
- Meio Externo de Gravação → contrasta com → [[Memória]]
- Meio Externo de Gravação → transfere dados para → [[Memória]] (via [[DMA]] ou via [[Processador]])

## Categorias de Dispositivos

### Sistemas de armazenamento de dados
Persistem dados além da execução — correspondem ao bloco **R** da figura de Von Neumann.

| Tecnologia | Exemplos |
|---|---|
| Cartões perfurados | (histórico) |
| Fitas magnéticas | (histórico / backup) |
| Discos magnéticos | Disquetes, discos rígidos (HD) |
| Discos ópticos | CD, DVD, Blu-ray |
| Memória não-volátil | SSDs, pen drives |

> [!warning] Armazenamento ≠ Memória
> Embora SSDs e pen drives sejam construídos com chips de memória internamente, sua **função** é de armazenamento (bloco R). [[Memória]] (bloco M) é exclusivamente RAM e ROM. Ver [[Memória#Distinção Memória vs Armazenamento]].

### Dispositivos de entrada
Permitem a **entrada ou leitura** de dados pelo computador.
- Exemplos: teclado, mouse, joystick, scanner, microfone, webcam.

### Dispositivos de saída
Permitem a **saída ou gravação** de dados pelo computador.
- Exemplos: impressora, monitor de vídeo, caixa de som, fone de ouvido.

### Dispositivos bidirecionais
Operam como entrada **e** saída simultaneamente.
- Exemplos: porta de rede, USB, SATA.

## Acesso ao Processador

Para que dados armazenados em dispositivos externos possam ser processados, eles precisam primeiro ser **transferidos para a [[Memória]]**. Há dois caminhos para isso:

| Caminho | Método | Eficiência |
|---|---|---|
| "Possível" | Via [[Processador]] (CC) | Simples, mas ocupa o processador durante a transferência |
| "Preferível" | [[DMA]] — direto entre R e M | Complexo, mas o processador fica livre para outras tarefas |

## Ver também
- [[Memória]]
- [[Processador]]
- [[DMA]]
- [[Modelo de Von Neumann]]
- [[Hardware]]
