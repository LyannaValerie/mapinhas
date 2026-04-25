---
title: Dalvik
aliases:
  - máquina virtual Dalvik
  - Dalvik VM
  - bytecode Dalvik
  - DEX
tags:
  - computação/sistemas
source: "Sistemas Operacionais Modernos, cap. 10"
created_by: Codex
---

# Dalvik

## Definição

Máquina virtual usada pelo Android para executar aplicações empacotadas em bytecode próprio (DEX — Dalvik Executable), baseado em máquina de **registradores** em vez da máquina de pilha do Java tradicional.

## Papel técnico

Dalvik adapta o modelo Java para dispositivos com memória e energia limitadas. Não há troca de páginas (swap) — apenas paginação por demanda de código (limpas, da flash). Páginas sujas devem ficar bloqueadas na RAM.

## Relações (SPO)

- Dalvik -> executa -> bytecode DEX
- Aplicação Android -> compila em -> bytecode Java → converte para -> DEX
- Processo Android -> contém -> instância Dalvik própria
- Dalvik -> depende de -> processos Linux para isolamento e segurança
- Zygote -> inicializa -> Dalvik e pré-carrega classes
- Caixa de areia -> é implementada por -> processo Linux (não pelo Dalvik)

## Pipeline de Compilação

```
Código Java (.java)
    ↓ javac
Bytecode Java (.class)
    ↓ dex (ferramenta Android)
Bytecode Dalvik (.dex)
    ↓ empacota em .apk
Instalado no dispositivo
    ↓ JIT (em tempo de execução)
Código nativo
```

## Características Técnicas

| Aspecto | Dalvik | JVM tradicional |
|---------|--------|-----------------|
| Modelo | Máquina de registradores | Máquina de pilha |
| Formato | DEX (compacto) | CLASS |
| Pool de strings | Compartilhado entre classes | Por arquivo .class |
| Compilação | JIT (Dalvik) / AOT (ART) | JIT |
| Processo | Um por app | Gerenciado pelo SecurityManager |

## Dalvik vs ART

- **Dalvik**: compilação **JIT** (Just-In-Time) — código compilado durante execução
- **ART** (Android RunTime, substituiu Dalvik): compilação **AOT** (Ahead-Of-Time) — código compilado na instalação → execução mais rápida, mas instalação mais lenta

## Zygote

Problema: inicializar Dalvik + pré-carregar classes leva ~1 segundo. Android precisa lançar apps em 200 ms.

**Solução**: `zygote` é um processo daemon que inicializa Dalvik e pré-carrega todas as classes comuns. Para lançar um app, o sistema faz `fork(zygote)`:
- Filho obtém Dalvik já inicializado via CoW
- Classes pré-carregadas são compartilhadas até escrita
- Novo código específico do app é carregado sobre isso

O Dalvik usa processos Linux (não SecurityManager Java) para isolamento: cada app tem seu UID e processo próprio.

## Integração com Linux

O Android delega ao Linux (não ao Dalvik) os aspectos críticos:
- Isolamento de memória
- Limpeza de recursos ao término do processo
- Permissões e segurança (UID por app)
- Código nativo (NDK) pode ser usado diretamente

## Ver também

- [[Android]]
- [[Binder]]
- [[Processos no Linux]]
- [[Segurança no Linux]]
