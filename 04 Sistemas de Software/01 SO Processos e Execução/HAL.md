---
title: HAL
aliases:
  - Hardware Abstraction Layer
  - camada de abstração de hardware
tags:
  - computação/sistemas
source: "Operating Systems: From 0 to 1, cap. 9"
created_by: Codex
---

# HAL

Nota criada pelo Codex.

## Definição

**HAL** (*Hardware Abstraction Layer*) é a camada de interfaces que separa o núcleo do sistema operacional dos detalhes específicos de dispositivos. Ela permite que drivers implementem contratos estáveis sem expor toda a variação do hardware ao restante do sistema.

## Finalidade

- reutilizar a mesma API de software sobre dispositivos diferentes
- isolar bugs entre aplicação, kernel e driver
- reduzir acoplamento com protocolos específicos de hardware

## Relações (SPO)

- HAL → define → interface entre kernel e drivers
- [[Driver]] → implementa → contrato da HAL
- [[Kernel]] → usa → HAL para acessar hardware
- HAL → reduz → dependência direta de dispositivo específico

