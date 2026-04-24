---
title: Shell
aliases:
  - shell
  - command-line interpreter
  - interpretador de linha de comando
  - terminal
tags:
  - computação/fundamentos
date: 2026-04-06
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Sistemas de Software]]

# Shell

## Definição
Programa que implementa um **interpretador de linha de comando**: exibe um prompt, aguarda o usuário digitar um comando, executa-o e repete o ciclo.

## Relações (SPO)
- Shell → é um → programa de aplicação do [[Sistema Operacional]]
- Shell → interpreta → comandos digitados pelo usuário
- Shell → carrega e executa → programas executáveis
- Shell → utiliza → [[DMA]] indiretamente ao carregar executáveis do disco para memória

## Funcionamento

```
1. Exibe prompt  (ex: linux> )
2. Aguarda comando do usuário
3. Se é comando built-in → executa diretamente
4. Se não é built-in → assume que é executável no disco
   → carrega o arquivo da memória de massa para a RAM
   → transfere controle para o programa
5. Aguarda término do programa
6. Volta ao passo 1
```

## Exemplo — executando hello

```bash
linux> ./hello
hello, world
linux>
```

Ao pressionar Enter, o shell:
1. Reconhece `./hello` como nome de executável (não é built-in)
2. Carrega o arquivo `hello` do disco para a [[Memória]] principal via [[DMA]]
3. Transfere controle para as instruções do programa `hello`
4. O programa imprime `hello, world` no display e encerra
5. O shell retorna ao prompt

> [!note] Como os caracteres do comando chegam à memória
> Enquanto o usuário digita `./hello` no teclado, cada caractere é lido pelo processador em um [[Registrador]] e então armazenado na [[Memória]] — um byte por vez.

## Ver também
- [[Sistema Operacional]] — o shell é a interface de usuário do SO
- [[Sistema de Compilação]] — produz os executáveis que o shell carrega
- [[DMA]] — mecanismo usado para carregar o executável do disco à memória
- [[Memória]] — destino do executável carregado
- [[Processador]] — executa as instruções após o carregamento
