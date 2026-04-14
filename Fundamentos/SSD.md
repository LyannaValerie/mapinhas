---
title: SSD
aliases:
  - Solid State Drive
  - NVMe
  - AHCI
  - wear leveling
  - balanceamento de desgaste
  - amplificação de escrita
  - write amplification
  - coleta de lixo
  - garbage collection
  - TRIM
  - overprovisioning
  - espaço reservado
  - TBW
  - RAIN
  - controlador SSD
tags:
  - computação/fundamentos
  - computação/armazenamento
  - computação/hardware
date: 2026-04-12
source: "Livro Hardware clubedohardware.com.br — Cap. 48"
---

# SSD

Solid State Drive. Substituto eletrônico do [[Disco Rígido]]: sem partes mecânicas, baseado em [[Memória Flash]] (NAND) ou 3D XPoint/Optane. Conectado via [[Portas de Comunicação]] (SATA, PCIe/NVMe, USB).

| | SSD | HD |
|---|---|---|
| Velocidade | Muito maior | Menor |
| Ruído | Nenhum | Sim |
| Consumo | Menor | Maior |
| Preço/GB | Maior | Menor |
| Capacidade máxima | Menor | Maior |
| Vida útil | Limitada (P/E cycles) | Mecânica (impactos) |

> [!note] Nomenclatura
> SSD não é "disco" — componente 100% eletrônico. Termo correto para HD + SSD: "unidade de armazenamento".

---

## Formatos Físicos

| Formato | Interface | Desempenho | Uso |
|---|---|---|---|
| **2,5 polegadas** | SATA ou SAS | Menor | Entrada; upgrade de portátil |
| **M.2** | SATA ou PCIe (NVMe) | Médio–alto | Desktop e portátil moderno |
| **PCIe (placa de expansão)** | PCIe | Máximo | Estações de alto desempenho |
| **Externo** | USB | Variável | Portabilidade |

- M.2 SATA: desempenho inferior ao M.2 NVMe
- M.2 slots tipo **B** = PCIe x2; tipo **M** = PCIe x4; muitos SSDs compatíveis com ambos
- Versão do slot PCIe deve ser ≥ versão do SSD para não limitar desempenho
- Externo USB: usar ao menos USB 3.0 (USB 3.2 Gen 1)

---

## Protocolo de Comunicação

| Protocolo | Interface | Desenvolvido para |
|---|---|---|
| **AHCI** | SATA | HDs mecânicos — limitações do protocolo refletem limitações mecânicas |
| **NVMe** | PCIe | Flash — sem as limitações do AHCI; maior desempenho e mais recursos |

M.2 aceita ambos — verificar qual o SSD usa. NVMe = desempenho superior.

---

## Capacidade de Armazenamento

Rotulada em **base 10** (como HDs). Windows reporta em base 2 (GiB).  
Capacidade física dos chips > capacidade rotulada — diferença = **overprovisioning** (espaço reservado).

Exemplo:
- SSD rotulado 480 GB → chips físicos têm 512 GiB → espaço reservado = 512 GiB − 447 GiB = **65 GiB**
- SSD rotulado 512 GB → chips físicos têm 512 GiB → espaço reservado = 512 GiB − 476,8 GiB = **35,2 GiB**

---

## Problemas Específicos de Flash NAND

> [!info] 3D XPoint / Optane não sofre estes problemas
> Pode apagar e gravar bit a bit (NAND: página para escrita, bloco para apagamento). Intel descontinuou Optane em julho/2022.

### Amplificação de Escrita

Flash NAND: escrita em **páginas** (2–8 KiB) + apagamento em **blocos** (256 KiB – 1 MiB).

Problema: gravar 4 KiB pode exigir apagar 256 KiB → 63× de dados apagados desnecessariamente.

$$\text{Fator de amplificação} = \frac{\text{tamanho do bloco}}{\text{tamanho dos dados gravados}}$$

- Escrita **sequencial**: blocos preenchidos completamente → fator ≈ 1:1
- Escrita **aleatória**: blocos parcialmente preenchidos → fator alto → degrada vida útil

### Coleta de Lixo (Garbage Collection)

Atualização de arquivo: SSD **não sobrescreve** — marca páginas antigas como **inválidas** e grava dados novos em páginas vazias.

Processo GC (periódico):
1. Bloco com mix de páginas válidas + inválidas
2. Páginas válidas → movidas para bloco com páginas vazias disponíveis
3. Bloco original → pode ser apagado
4. Balanceamento de desgaste executado em conjunto

### TRIM

Comando do SO ao SSD informando quais páginas podem ser marcadas inválidas (arquivo apagado / formatação).

Sem TRIM: GC move páginas "apagadas" pelo usuário como se fossem válidas → desperdício de ciclos + espaço.  
Com TRIM: GC ignora páginas informadas → mais rápido, mais espaço livre, maior vida útil.

TRIM automático a partir do **Windows 7**.

---

## Espaço Reservado (Overprovisioning)

10–20% da capacidade recomendado. Configurável em alguns SSDs.

Funções:
- Garante blocos apagados disponíveis → usuário não espera apagamento antes de gravar
- Suporte ao GC e wear leveling
- Substitui células defeituosas

> [!note] Localização não fixa
> Wear leveling move dados continuamente — área "reservada" e área "disponível" se alternam fisicamente.

---

## Vida Útil (Endurance)

Medida em **TBW** (TeraBytes Written). Nem todos os fabricantes divulgam.

Ciclos de P/E por tipo de memória — ver [[Memória Flash#Vida Útil (P/E Cycles)]].

| Tipo | Ciclos P/E | Uso recomendado |
|---|---|---|
| SLC | 100.000 | Enterprise; cache de SSD |
| MLC | 30.000 | Desktop/enterprise |
| TLC | 3.000 | Desktop comum |
| QLC | 1.000 | Evitar para uso intenso |

**Fatores que reduzem vida útil:**
- Gravação de muitos arquivos pequenos
- Escritas aleatórias (alta amplificação)
- SO sem TRIM (pré-Win7)
- Overprovisioning insuficiente
- Chips QLC

**Estimativa Intel**: usuário médio grava ~5 GB/dia. SSD de 40 TBW → ~22 anos de uso médio.

> [!warning] Recuperação de dados em SSD
> TRIM + GC apagam páginas efetivamente → dados irrecuperáveis mesmo sem sobrescrever. Diferente de HD (dados permanecem até sobrescrição). Queima de SSD: recuperação muito mais difícil que HD (dados magnéticos em pratos vs elétrico em chips).

---

## Desfragmentação

**Não desfragmentar SSDs.** Motivos:
- SSD é eletrônico → localização física irrelevante para desempenho
- Desfragmentar = ciclos extras de apagamento/escrita → reduz vida útil

---

## Arquitetura

### Controlador
- Intermediário entre computador e chips de memória
- Gerencia GC, wear leveling, TRIM, ECC
- **Canais paralelos**: acessa múltiplos chips simultaneamente (similar a RAID 0 interno) → mais canais = mais rápido
- Alguns controladores usam **compressão de dados**: acelera transferência de dados compressíveis; sem ganho (ou pior) em dados já comprimidos (JPG, MP3, vídeo)

### Cache

| Tipo | Observação |
|---|---|
| Nenhum | SSDs de baixo custo |
| DRAM | Mais rápido; cache de metadados e mapeamento |
| NAND SLC | Mais lento que DRAM; mais barato |
| 3D XPoint / Optane | Raro; alto desempenho |

### Memória
Chips NAND (SLC/MLC/TLC/QLC) ou 3D XPoint. Tipo determina vida útil, desempenho e preço.

### RAIN (enterprise)
Redundant Array of Independent NAND. Proteção similar ao RAID 5 entre chips internos do SSD. Disponível em alguns modelos enterprise.

---

## Desempenho

| Métrica | Unidade | O que mede |
|---|---|---|
| Leitura/escrita sequencial | MB/s | Transferência de arquivos grandes |
| Leitura/escrita aleatória 4KiB | IOPS | Acesso a muitos arquivos pequenos (SO, apps) |

IOPS = I/O Operations Per Second. Mais relevante para uso do dia-a-dia (SO carregado de setores 4KiB aleatórios).

---

## Relações (SPO)

- SSD → usa → [[Memória Flash]] NAND (ou 3D XPoint) sem partes mecânicas
- NVMe → supera → AHCI por ser projetado para flash, não para HD mecânico
- Amplificação de escrita → ocorre em → escritas aleatórias menores que o bloco
- TRIM → reduz → amplificação de escrita; aumenta desempenho e vida útil
- Coleta de lixo → apaga → blocos com páginas inválidas; move páginas válidas antes
- Wear leveling → distribui → escritas entre todas as células; prolonga vida útil
- Overprovisioning → garante → blocos apagados disponíveis; troca capacidade por desempenho/vida útil
- Controlador → acessa → chips em paralelo (canais); similar a RAID 0 interno
- Desfragmentação → não aplicar → consome P/E cycles sem benefício em SSD

---

## Ver também

- [[Memória Flash]] — NAND: SLC/MLC/TLC/QLC, P/E cycles, páginas e blocos
- [[Disco Rígido]] — alternativa mecânica; geometria, desfragmentação, interfaces
- [[Memória de Massa]] — conceito geral de armazenamento persistente
- [[RAID]] — arranjos de múltiplas unidades; RAIN interno do SSD segue princípio RAID 5
- [[Pen Drive]] — SSD removível via USB; mesma arquitetura e problemas
- [[Portas de Comunicação]] — SATA, PCIe/M.2, USB (interfaces físicas)
- [[Slots de Expansão]] — M.2 e PCIe como slots de SSD
- [[Unidades Métricas]] — base 10 (GB) vs base 2 (GiB)
