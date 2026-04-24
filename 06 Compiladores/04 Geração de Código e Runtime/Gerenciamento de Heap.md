---
title: Gerenciamento de Heap
tags:
  - compiladores
  - procedimentos
  - memória
aliases:
  - heap management
  - garbage collection
  - coleta de lixo
  - alocação dinâmica
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Compiladores]]

# Gerenciamento de Heap

Mecanismos para alocar e liberar memória com **tempo de vida arbitrário** — além do escopo de uma única chamada de procedimento.

> [!info] Contexto
> Seção 6.6 de *Engineering a Compiler*. Parte do cap. 6 sobre [[Abstração de Procedimento]].

## Por que Heap?

A pilha de execução suporta ARs com tempo de vida limitado à duração da chamada. Objetos que precisam sobreviver ao retorno do procedimento criador exigem heap.

## Gerenciamento Explícito

O programador aloca e libera manualmente: `malloc`/`free` em C, `new`/`delete` em C++.

**Problemas**:
- **Dangling pointer**: uso de memória já liberada
- **Memory leak**: memória alocada e nunca liberada
- **Double free**: liberar o mesmo bloco duas vezes

**Implementação típica**:
- Lista de blocos livres; `malloc` = encontrar bloco adequado (first-fit, best-fit)
- Fragmentação interna e externa são desafios
- `free` retorna bloco à lista; coalescência de blocos adjacentes livres

## Gerenciamento Implícito (Coleta de Lixo)

O runtime detecta e libera memória inacessível automaticamente. Programador não chama `free`.

### Mark-and-Sweep

1. **Mark**: a partir de raízes (variáveis locais, globais, registradores), marca todos os objetos alcançáveis via ponteiros
2. **Sweep**: varre todo o heap; libera objetos não marcados

- Pausa durante GC (*stop-the-world*)
- Não compacta por padrão

### Reference Counting

Cada objeto mantém contador de referências. Contador chega a zero → objeto liberado imediatamente.

- Problema: **ciclos** → objetos em ciclo nunca chegam a zero
- Requer detecção de ciclos (Python usa mark-and-sweep para ciclos)

### Copying/Compacting GC

Copia objetos vivos para nova área (*from-space* → *to-space*). Heap fica compacto; alocação é simples incremento de ponteiro.

- Custo proporcional a objetos **vivos** (não ao lixo)
- Generational GC: a maioria dos objetos morre jovem → coletar mais a geração jovem

### GC em Compiladores

O compilador deve:
- Manter **stack maps** (ou GC roots): informação sobre quais slots do AR contêm ponteiros
- Garantir que ponteiros sejam sempre atualizados após compactação

## Heap vs. Pilha — Decisão do Compilador

Em linguagens funcionais ou com closures, variáveis de escopo externo podem **escapar** para além do escopo declarante. O compilador detecta esse *escape* e aloca no heap em vez da pilha.

## Relações

- [[Abstração de Procedimento]] — pilha de ARs; heap é complemento
- [[Espaço de Nomes e Escopo Léxico]] — escape analysis determina onde alocar
- [[Forma de Código — Expressões]] — `new` gera chamada de runtime para alocação
- [[Back End do Compilador]] — gera stack maps para o GC
