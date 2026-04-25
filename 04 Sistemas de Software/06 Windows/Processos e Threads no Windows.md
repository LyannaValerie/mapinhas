---
title: Processos e Threads no Windows
aliases:
  - processos no Windows
  - threads no Windows
  - jobs no Windows
  - fibers no Windows
tags:
  - computação/sistemas
  - sistemas-operacionais/windows
source: "Sistemas Operacionais Modernos, cap. 11"
created_by: Codex
---

# Processos e Threads no Windows

## Definição

No Windows, [[Processo|processos]] são contêineres de recursos: espaço de endereçamento virtual, tabela de descritores e [[Thread|threads]]. Threads são as unidades escalonadas para execução na CPU.

## Processo

Cada processo possui:
- **Espaço de endereçamento virtual** próprio
- **Tabela de descritores** (handles para objetos do núcleo)
- **Token de segurança** compartilhado pelas threads
- **PEB** (Process Environment Block): lista de módulos carregados (EXE + DLLs), heap, variáveis de ambiente, diretório de trabalho

A estrutura interna do núcleo é `EPROCESS`, alocada no espaço do núcleo.

## Thread

Unidade de escalonamento. Cada thread possui:
- Contexto de registradores e pilha em modo usuário
- Pilha em modo núcleo (usada durante chamadas de sistema)
- **TEB** (Thread Environment Block): dados de modo usuário por thread
- Prioridade numérica (0–31); 0 = thread de zeragem de páginas ociosas

A estrutura interna do núcleo é `ETHREAD`.

### Prioridades

Organização em duas dimensões:
1. **Classe de prioridade do processo**: Idle, Below Normal, Normal, Above Normal, High, Realtime
2. **Prioridade relativa do thread**: Idle, Lowest, Below Normal, Normal, Above Normal, Highest, Time Critical

Prioridade efetiva = classe de processo + ajuste relativo do thread.

## Job

Agrupa processos para impor quotas compartilhadas: tempo de CPU, número de processos, uso de memória, acesso a UIDs.

## Fiber (filamento)

Unidade cooperativa gerenciada em modo usuário. Não escalonada diretamente pelo núcleo. `SwitchToFiber` troca explicitamente entre filamentos. Útil para portar código legado com coroutines.

## Criação de processo

Win32: `CreateProcess` → `kernel32.dll` → `NtCreateUserProcess` (núcleo). Etapas principais:

1. Abre o arquivo executável (PE)
2. Cria objeto de processo com espaço de endereçamento
3. Mapeia o executável e DLLs básicas
4. Cria thread inicial com pilha e TEB
5. Notifica o subsistema Win32 (`csrss.exe`)
6. Thread inicial executa loader (`ntdll!LdrInitializeThunk`) e chama `main`/`WinMain`

Sem equivalente a `fork` do UNIX: no Windows, criação sempre parte de imagem executável.

## Relações (SPO)

- Processo no Windows -> contém -> [[Thread|threads]]
- Processo no Windows -> possui -> espaço de endereçamento virtual
- Thread no Windows -> executa em -> [[Escalonador]]
- Job no Windows -> agrupa -> processos com quotas
- `CreateProcess` -> invoca -> `NtCreateUserProcess`

