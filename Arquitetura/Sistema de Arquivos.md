---
title: Sistema de Arquivos
aliases:
  - file system
  - arquivo
  - i-node
  - inode
  - diretório
  - NTFS
  - MFT
  - Master File Table
  - FAT
  - File Allocation Table
  - unidade de alocação
  - cluster
  - lista de livres
  - mapa de bits
  - descritor de arquivo
  - file descriptor
tags:
  - computação/arquitetura
date: 2026-04-13
---

# Sistema de Arquivos

## Definição
Abstração do nível OSM ([[Sistema Operacional]]) que organiza dispositivos de armazenamento como coleções de **arquivos** e **diretórios**, isolando o programador dos detalhes de setores, trilhas e controladores de disco.

## Relações (SPO)
- Sistema de Arquivos → implementa → abstração de arquivo
- Arquivo → é → sequência linear de bytes
- Diretório → agrupa → arquivos e subdiretórios em hierarquia de árvore
- i-node → armazena → metadados + localização física do arquivo (UNIX)
- MFT → armazena → metadados de todos os arquivos do volume (NTFS/Windows)
- Sistema de Arquivos → rastreia espaço livre via → lista de livres ou mapa de bits

---

## A abstração de arquivo

Do ponto de vista do programador, um arquivo é uma **sequência de bytes** numerados a partir de 0. Nenhuma estrutura é imposta pelo SO — qualquer organização (registros, linhas, campos) é responsabilidade do programa de aplicação.

Associado com todo arquivo aberto há um **ponteiro de posição** que indica o próximo byte a ler/escrever. `read`/`write` avançam o ponteiro; `lseek`/`SetFilePointer` reposicionam arbitrariamente.

### Tipos de arquivo (UNIX)

| Tipo | Descrição |
|---|---|
| **Normal** | Sequência arbitrária de bytes |
| **Especial de bloco** | Dispositivo de armazenamento (disco) — acesso por bloco |
| **Especial de caractere** | Dispositivo serial (teclado, impressora) — acesso byte a byte |
| **Diretório** | Arquivo especial que lista pares (nome, número de i-node) |

---

## Chamadas de sistema de arquivos (UNIX)

| Chamada | Significado |
|---|---|
| `creat(name, mode)` | Cria arquivo; mode especifica permissões |
| `open(name, mode)` | Abre/cria arquivo; retorna **descritor de arquivo (fd)** |
| `close(fd)` | Fecha o arquivo; libera o descritor |
| `read(fd, buffer, count)` | Lê `count` bytes para `buffer`; retorna bytes lidos |
| `write(fd, buffer, count)` | Escreve `count` bytes de `buffer` |
| `lseek(fd, offset, w)` | Reposiciona ponteiro de arquivo |
| `stat(name, buffer)` | Retorna metadados: tamanho, datas, permissões, proprietário |
| `chmod(name, mode)` | Altera permissões |
| `fcntl(fd, cmd, ...)` | Bloquear região; operações diversas |
| `unlink(name)` | Remove entrada de diretório; apaga arquivo se contagem de links = 0 |

**Descritores especiais:** fd=0 (stdin), fd=1 (stdout), fd=2 (stderr). Podem ser redirecionados.

### Equivalentes Windows 7 (API Win32)

| Win32 | UNIX | Significado |
|---|---|---|
| `CreateFile` | `open` | Cria ou abre arquivo; retorna **handle** |
| `DeleteFile` | `unlink` | Remove arquivo |
| `CloseHandle` | `close` | Fecha handle |
| `ReadFile` | `read` | Lê dados |
| `WriteFile` | `write` | Escreve dados |
| `SetFilePointer` | `lseek` | Reposiciona ponteiro |
| `GetFileAttributes` | `stat` | Retorna metadados |
| `LockFile/UnlockFile` | `fcntl` | Bloqueia/desbloqueia região |

> [!note] Handle vs. descritor de arquivo
> Descritores UNIX são inteiros pequenos (quase sempre < 20). Handles do Windows 7 não são inteiros pequenos — identificam todos os objetos do núcleo, dos quais podem existir milhões.

---

## Diretórios

Diretórios agrupam arquivos em **hierarquia de árvore**. Cada entrada de diretório contém: **nome do arquivo** + **número de i-node** (UNIX) ou referência equivalente (Windows).

### Chamadas de gerenciamento (UNIX)

| Chamada | Significado |
|---|---|
| `mkdir(name, mode)` | Cria diretório |
| `rmdir(name)` | Remove diretório vazio |
| `opendir(name)` | Abre para leitura de entradas |
| `readdir(ptr)` | Lê próxima entrada |
| `closedir(ptr)` | Fecha diretório |
| `chdir(dirname)` | Muda diretório de trabalho corrente |
| `link(name1, name2)` | Cria atalho: name2 aponta para o mesmo i-node de name1 |
| `unlink(name)` | Remove entrada; arquivo apagado se contagem de links = 0 |

**Caminhos absolutos:** `/usr/ast/data` — a partir do diretório raiz.
**Caminhos relativos:** `bin/game3` — a partir do diretório de trabalho corrente.

**Estrutura típica UNIX:**
```
/ (raiz)
├── bin/      (programas de uso frequente)
├── dev/      (arquivos especiais de dispositivos de E/S)
├── lib/      (bibliotecas)
└── usr/
    ├── ast/
    │   ├── data
    │   ├── foo.c
    │   └── bin/
    │       ├── game1
    │       └── game2
    └── jim/
```

---

## Implementação UNIX: i-nodes

Cada arquivo (incluindo diretórios) tem um **i-node** de 64 bytes. Os i-nodes ficam no início do disco (ou no início de cada grupo de cilindros). Dado um número de i-node, o sistema calcula diretamente seu endereço.

### Campos do i-node

1. Tipo do arquivo + bits de proteção RWX (9 bits) + bits especiais
2. Número de links (quantas entradas de diretório apontam para este arquivo)
3. Identidade do proprietário
4. Grupo do proprietário
5. Comprimento em bytes
6. **13 endereços de disco**
7. Timestamp de último acesso
8. Timestamp de última escrita
9. Timestamp de última modificação do i-node

### Esquema de endereçamento (bloco = 1 KB)

| Endereços | Modo | Arquivos até |
|---|---|---|
| 0–9 | **Diretos** (10 blocos) | 10.240 bytes |
| 10 | **Indireto simples** (256 blocos) | 272.384 bytes |
| 11 | **Indireto duplo** (256 × 256 blocos) | 67.381.248 bytes |
| 12 | **Indireto triplo** (256³ blocos) | ~17,2 GB |

> [!example]
> Arquivo de 50 KB: blocos diretos cobrem 10 KB; bloco indireto simples cobre os 40 KB restantes (40 blocos de 1 KB). Sem precisar de indireto duplo.

### Abertura de arquivo por caminho

```
open("/usr/ast/data") →
  1. i-node de "/" → lê diretório raiz → acha "usr" → número de i-node de /usr
  2. i-node de /usr → lê diretório → acha "ast" → número de i-node de /usr/ast
  3. i-node de /usr/ast → lê diretório → acha "data" → número de i-node do arquivo
  4. i-node do arquivo entra em tabela interna na memória → pronto para leitura/escrita
```

### Permissões RWX

Três grupos de 3 bits: `proprietário | grupo | outros`.

Exemplo `RWX R-X --X`: proprietário lê/escreve/executa; grupo lê/executa; outros só executam — outros podem usar o programa mas não copiá-lo (sem permissão de leitura).

---

## Implementação Windows 7: NTFS e MFT

O **NTFS** (NT File System) usa a **MFT (Master File Table)** — análoga ao conjunto de i-nodes do UNIX, mas armazenada como arquivo no volume (não em posição fixa).

### Características do NTFS
- Nomes de arquivo: até 255 caracteres Unicode; case-sensitive
- Ponteiro de arquivo: 64 bits (arquivos de até 2⁶⁴ bytes)
- Volume organizado em **clusters** de tamanho fixo (512 B a 64 KB)
- Clusters referenciados por deslocamento de 64 bits desde o início do volume

### Estrutura da MFT

| Componente | Conteúdo |
|---|---|
| **Cabeçalho** | Ponteiros para diretório raiz, arquivo de boot, blocos defeituosos, lista de livres |
| **Entrada por arquivo** | 1 KB por arquivo/diretório (exceto se cluster ≥ 2 KB) |

### Campos de uma entrada MFT

| Campo | Conteúdo |
|---|---|
| **Informações padrão** | Timestamps POSIX, contagem de hard links, bits somente-leitura/arquivamento |
| **Nome do arquivo** | Até 255 caracteres Unicode |
| **Nome MS-DOS** | Nome 8+3 para compatibilidade (omitido se o nome já obedece a 8+3) |
| **Segurança** | Ponteiro para arquivo centralizado de segurança (desde Windows 2000) |
| **Dados** | Pequenos: **inline** na entrada MFT. Grandes: carreiras de clusters (run-length) |

**Arquivo imediato:** dados pequenos ficam na entrada MFT — evita acesso extra ao disco.
**Carreiras de clusters:** um único número de cluster + comprimento representa sequências contíguas — eficiente para arquivos grandes.

---

## Estratégias de alocação em disco

### Alocação consecutiva
- Arquivo ocupa setores contíguos
- Simples: basta o primeiro setor e o comprimento
- **Limitação:** tamanho máximo deve ser conhecido de antemão; arquivo não pode crescer
- **Uso:** CD-ROMs (tamanhos definitivos antes da gravação)

### Alocação não-consecutiva
- Blocos espalhados pelo disco; estrutura de índice aponta para cada bloco
- Permite arquivos crescentes sem realocar
- **Uso:** discos rígidos, SSDs

**Índice UNIX:** i-node com endereços diretos/indiretos.
**FAT (MS-DOS/Windows antigo):** tabela em memória com índice de sucessores. `FAT[4]=52, FAT[52]=19, FAT[19]=-1` → arquivo ocupa blocos 4, 52, 19.
**NTFS:** carreiras de clusters consecutivos com encoding run-length — eficiente e compacto.

---

## Gerenciamento de espaço livre

### Lista de livres
- Lista de lacunas: cada entrada = (endereço de início, número de setores)
- Fácil encontrar lacuna de tamanho específico
- **Desvantagem:** tamanho varia conforme arquivos são criados/destruídos

### Mapa de bits
- 1 bit por unidade de alocação: 1 = ocupada, 0 = livre
- Tamanho **constante** — não varia com o estado do disco
- Mudar estado = mudar 1 bit (rápido)
- **Desvantagem:** difícil encontrar bloco de tamanho específico

---

## Tamanho da unidade de alocação

Trade-off fundamental:

| Unidade grande | Unidade pequena |
|---|---|
| Menos entradas de índice / tabela | Mais entradas → tabelas maiores na memória |
| Transferências de disco mais eficientes | Conjunto de trabalho ocupa menos RAM |
| Mais fragmentação interna (desperdício na última unidade de cada arquivo) | Menos desperdício por arquivo |

**Tendência atual:** unidades maiores (mínimo 4 KB) — custo do disco caiu; importa mais a velocidade de transferência.

> [!example] Problema histórico
> MS-DOS com partição de 2 GB usava unidades de 32 KB. Um arquivo de 100 bytes desperdiçava 32.668 bytes de espaço em disco.

---

## Ver também
- [[Sistema Operacional]] — sistema de arquivos é uma das três abstrações fundamentais do SO
- [[DMA]] — transferências de bloco entre disco e memória
- [[Memória Virtual]] — arquivos mapeados em memória: `mmap` (UNIX), `CreateFileMapping` (Windows)
- [[Interrupção]] — controlador de disco gera interrupção ao concluir operação
- [[Processo]] — cada processo mantém tabela de descritores de arquivo abertos
- [[Hierarquia de Memória]] — disco na base da hierarquia de memória/armazenamento
- [[Meio Externo de Gravação]] — dispositivos físicos abstraídos pelo sistema de arquivos
