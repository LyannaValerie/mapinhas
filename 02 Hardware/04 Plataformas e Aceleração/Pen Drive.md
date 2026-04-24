---
title: Pen Drive
aliases:
  - USB stick
  - flash drive
  - thumb drive
  - USB drive
  - memória USB
tags:
  - computação/fundamentos
  - computação/armazenamento
  - computação/hardware
date: 2026-04-12
source: "Livro Hardware clubedohardware.com.br — Cap. 50"
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Hardware]]

# Pen Drive

Dispositivo de armazenamento removível baseado em [[Memória Flash]] NAND, conectado via USB. Funcionalmente equivalente a um [[SSD]] portátil — mesma arquitetura, mesmos problemas e soluções.

> [!note] Nomenclatura
> "Pen drive" = invenção brasileira. Em inglês: **USB stick**, USB memory, USB drive, **flash drive** ou **thumb drive**.

---

## Arquitetura

Dois componentes internos:

1. **Chip controlador** — intermediário entre USB e memória; gerencia GC, wear leveling, canais paralelos; pode ter criptografia
2. **Chips NAND** — mesmos princípios de [[SSD]]: páginas/blocos, P/E cycles, TBW

Alguns modelos: interruptor físico de **proteção contra escrita**.

> Todo o conteúdo do capítulo [[SSD]] aplica-se integralmente a pen drives (wear leveling, garbage collection, TRIM, amplificação de escrita, vida útil, não desfragmentar).

---

## Desempenho

- Depende dos **chips** + **versão USB** da porta
- Porta do computador deve ser ≥ versão do pen drive
- Largura de banda máxima do USB raramente atingida (especialmente em modelos baratos)
- Controladores com múltiplos canais acessam chips em paralelo → maior desempenho

---

## Segurança

| Recurso | Proteção real |
|---|---|
| Criptografia por hardware no controlador | **Sim** — dados inacessíveis sem chave |
| Leitor de impressão digital + criptografia | **Sim** |
| Senha / impressão digital **sem** criptografia | **Não** — bypass trivial por usuário técnico |

> [!danger] Falsa segurança
> Modelos baratos com senha ou leitor biométrico mas **sem criptografia** não protegem dados. Acesso direto aos chips contorna a autenticação.

---

## Relações (SPO)

- Pen drive → usa → [[Memória Flash]] NAND + controlador USB
- Pen drive → equivale a → [[SSD]] em formato removível
- Pen drive → conecta via → [[Portas de Comunicação#USB|USB]]
- Criptografia por hardware → protege → dados mesmo com acesso físico aos chips
- Senha sem criptografia → não protege → dados de usuário técnico

---

## Ver também

- [[SSD]] — arquitetura, vida útil, GC, TRIM, wear leveling (integralmente aplicáveis)
- [[Memória Flash]] — NAND: base tecnológica
- [[Portas de Comunicação]] — USB: interface de conexão
