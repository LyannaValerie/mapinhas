---
title: Espaço de Usuário e Espaço de Kernel
aliases:
  - user space
  - kernel space
  - userspace
  - kernelspace
tags:
  - computação/sistemas
source: "Operating Systems: From 0 to 1, cap. 9"
created_by: Codex
---

# Espaço de Usuário e Espaço de Kernel

Nota criada pelo Codex.

## Definição

**Espaço de usuário** é o domínio de execução de aplicações; **espaço de kernel** é o domínio privilegiado onde o sistema operacional controla recursos críticos. A separação reduz dano por falhas e impede acesso direto não autorizado a hardware e memória.

## Relação com API do sistema

Aplicações não devem manipular hardware diretamente. Elas atravessam interfaces do sistema, como [[Chamada do Sistema]], para solicitar serviços ao kernel.

## Relações (SPO)

- Aplicação → executa em → espaço de usuário
- [[Kernel]] → executa em → espaço de kernel
- [[Chamada do Sistema]] → cruza → limite usuário-kernel
- Separação de privilégios → protege → recursos do sistema

