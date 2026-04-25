---
title: Falha de Página
aliases:
  - page fault
tags:
  - computação/sistemas
  - sistemas-operacionais/memória
source: "Sistemas Operacionais Modernos, cap. 3"
created_by: Codex
---

# Falha de Página

Nota criada pelo Codex.

## Definição

Exceção gerada quando um processo acessa uma página virtual que não está presente na [[Memória]] física ou viola suas permissões.

## Papel técnico

Em falta por ausência, o [[Sistema Operacional]] localiza a página no armazenamento secundário, escolhe um quadro livre ou uma vítima, atualiza a [[Tabela de Páginas]] e reinicia a instrução interrompida. Em falta por proteção, a execução normalmente é abortada ou sinalizada.

## Funcionamento

**Tratamento de falta por ausência (sequência típica):**

1. [[MMU]] detecta bit de presente = 0 → gera interrupção de hardware.
2. SO salva estado do processo (registradores, PC).
3. SO identifica qual endereço virtual causou a falta (registrador de falha de página ou bits de status da MMU).
4. SO verifica se o endereço é válido (está no espaço de endereçamento) e qual permissão se aplica. Se inválido → sinal de erro ao processo (ex: SIGSEGV no Linux).
5. Se válido: SO encontra um quadro físico livre. Se não há quadro livre → executa [[Algoritmo de Substituição de Páginas]] para escolher vítima.
6. Se página vítima está modificada (bit *dirty* = 1) → escreve vítima no disco antes de reutilizar o quadro.
7. SO inicia leitura da página ausente do disco para o quadro.
8. Enquanto espera E/S → outro processo pode executar na CPU.
9. Quando leitura completa: SO atualiza entrada na [[Tabela de Páginas]] (quadro, presente=1, modificada=0).
10. SO reinicia a instrução que falhou.

**Falta por proteção:** violação de permissão (escrita em página RO, execução em página de dados). Normalmente aborta o processo ou gera sinal.

## Relações (SPO)

- Falha de Página -> interrompe -> [[Processo]]
- Falha de Página -> aciona -> [[Gerenciamento de Memória]]
- Falha de Página -> pode chamar -> [[Algoritmo de Substituição de Páginas]]
- Falha de Página recorrente -> causa -> [[Thrashing]]
- [[Entrada de Tabela de Páginas]] -> sinaliza -> presença ou ausência da página

