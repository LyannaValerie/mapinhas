---
title: RAID
aliases:
  - Redundant Array of Independent Disks
  - RAID 0
  - RAID 1
  - RAID 5
  - RAID 6
  - RAID 10
  - RAID 0+1
  - RAID 50
  - RAID 60
  - JBOD
  - striping
  - divisão de dados
  - espelhamento
  - paridade RAID
  - hot spare
  - NAS
tags:
  - computação/fundamentos
  - computação/armazenamento
  - computação/hardware
date: 2026-04-12
source: "Livro Hardware clubedohardware.com.br — Cap. 49"
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Hardware]]

# RAID

Redundant Array of Independent Disks (ou Inexpensive Disks). Combina [[Disco Rígido|HDs]] ou [[SSD|SSDs]] para aumentar **desempenho** e/ou **confiabilidade**. Mais comum em servidores e NAS (Network Attached Storage); suportado em placas-mãe modernas via RAID por software.

**Três princípios:**

| Princípio | Objetivo | Técnica |
|---|---|---|
| **Divisão de dados** (striping) | Desempenho | Blocos intercalados entre unidades, leitura/escrita em paralelo |
| **Espelhamento** (mirroring) | Confiabilidade | Dados replicados em ≥2 unidades simultaneamente |
| **Paridade** | Confiabilidade | Dado calculado via XOR ou Reed-Solomon permite reconstruir unidade perdida |

> [!note] Nivelamento de capacidade
> Em qualquer modo RAID com unidades de capacidades diferentes: todas niveladas pela **menor** (Cmin).

> [!note] Hot spare
> Unidade ociosa pré-configurada. Ao detectar falha: assume automaticamente → reconstrução imediata sem intervenção humana. Pode ser **compartilhado** entre múltiplos arranjos gerenciados pela mesma controladora (exceto RAID 5E/5EE/6E, que têm espaço reserva distribuído).

---

## Modos RAID — Tabela Comparativa

| Modo | Unid. mín. | Capacidade total | Falhas toleradas | Técnica |
|---|---|---|---|---|
| **RAID 0** | 2 | n × Cmin | 0 | Striping puro |
| **RAID 1** | 2 | Cmin | n−1 | Mirroring puro |
| **RAID 0+1** | 4 (par) | n × Cmin / 2 | 1 unidade por conjunto RAID 0* | Striping → Mirroring |
| **RAID 10** | 4 (par) | n × Cmin / 2 | 1 por par RAID 1 (possivelmente 2+)** | Mirroring → Striping |
| **RAID 1E** | 3 (ímpar) | n × Cmin / 2 | 1 | Mirroring + Striping (não padronizado) |
| **RAID 100** | 8 | n × Cmin / 2 | 1 por arranjo RAID 1 | RAID 0 sobre RAID 10 |
| **RAID 2** | 3+ | (n−p) × Cmin*** | 1 | ECC Hamming por bit (teórico) |
| **RAID 3** | 3 | (n−1) × Cmin | 1 | Paridade por byte; unidade dedicada |
| **RAID 4** | 3 | (n−1) × Cmin | 1 | Paridade por bloco; unidade dedicada |
| **RAID 5** | 3 | (n−1) × Cmin | 1 | Paridade distribuída (XOR) |
| **RAID 5E/5EE** | 4 | (n−2) × Cmin | 1 (+ hot spare distribuído) | RAID 5 + reserva embutida |
| **RAID 50** | 6 | grupos × (k−1) × Cmin | 1 por grupo RAID 5 | RAID 0 sobre RAID 5 |
| **RAID 6** | 4 | (n−2) × Cmin | **2** | Dupla paridade (XOR + Reed-Solomon) |
| **RAID 6E** | 5 | (n−3) × Cmin | 2 (+ hot spare distribuído) | RAID 6 + reserva embutida |
| **RAID 60** | 8 | grupos × (k−2) × Cmin | **2** por grupo RAID 6 | RAID 0 sobre RAID 6 |
| **JBOD** | 2 | ΣC de todas | 0 | Concatenação; sem redundância |

*Se a unidade única do conjunto RAID 0 com falha for a última — o conjunto RAID 1 cobre.
**RAID 10 pode tolerar 2+ falhas se em pares diferentes.
***p = quantidade de unidades de paridade.

---

## Fórmulas de Capacidade

$$C_{RAID0} = C_{min} \cdot n$$

$$C_{RAID1} = C_{min}$$

$$C_{RAID0+1} = C_{RAID10} = C_{RAID1E} = C_{min} \cdot \frac{n}{2}$$

$$C_{RAID3} = C_{RAID4} = C_{RAID5} = C_{min} \cdot (n - 1)$$

$$C_{RAID6} = C_{RAID5E} = C_{min} \cdot (n - 2)$$

$$C_{RAID6E} = C_{min} \cdot (n - 3)$$

---

## Detalhamento dos Modos

### RAID 0
Striping puro. Arquivo dividido em blocos de tamanho fixo (configurável na criação; imutável depois) alternados entre unidades. **Qualquer falha = perda total dos dados.** Capacidade máxima, desempenho máximo.

### RAID 1
Mirroring puro. Dados idênticos gravados em paralelo em todas as unidades. Leitura pode ser paralela (algum ganho). Escrita sem ganho. Capacidade = 1 unidade independente de quantas houver no espelho.

### RAID 0+1 vs. RAID 10

Ambos combinam striping + mirroring com mesma fórmula de capacidade. Diferença estrutural:

| | RAID 0+1 | RAID 10 |
|---|---|---|
| Estrutura | RAID 0 → RAID 1 | RAID 1 → RAID 0 |
| 1 falha | Perde um conjunto RAID 0 inteiro | Perde apenas o par do espelho |
| Reconstrução | Copia para **2 unidades** do conjunto | Copia para **1 unidade** substituída |
| Confiabilidade | **Menor** | **Maior** |

> [!tip] Preferir RAID 10
> Mais confiável e reconstrução mais rápida. Use RAID 0+1 apenas se a controladora não suportar RAID 10.

### RAID 1E
RAID 10 com número ímpar de unidades. Não padronizado → implementações variam entre controladoras. Cada bloco espelhado em unidade adjacente; dois blocos legíveis simultaneamente (comportamento RAID 0). Tolera 1 falha; 2 falhas = perda de dados.

### RAID 100
RAID 0 por software sobre grupos RAID 10 por hardware. Mínimo 8 unidades. Falhas toleradas = número de grupos RAID 1, desde que em grupos distintos.

### RAID 2
Paridade ECC (código de Hamming) calculada bit a bit. Requer sincronização de rotação entre discos. **Nunca usado na prática.** Apenas teórico.

### RAID 3
Paridade por byte em unidade dedicada. Requer sincronização de rotação. Quase não usado. Substituído por RAID 5.

### RAID 4
Paridade por bloco em unidade dedicada. Sem sincronização necessária. Quase não usado. Substituído por RAID 5 (paridade distribuída elimina gargalo da unidade de paridade).

### RAID 5
Paridade distribuída (XOR) — sem unidade dedicada de paridade. Bloco de paridade rotaciona entre unidades a cada stripe. Configuração mais comum: **assíncrona esquerda** (paridade ao final do grupo; dados seguintes em unidade diferente da paridade anterior).

Resistência: 1 falha. Segunda falha antes da reconstrução = **perda total**.

**Hot spare compartilhável** entre múltiplos arranjos RAID 5 na mesma controladora.

### RAID 5E e 5EE
Hot spare distribuído entre unidades (sem unidade ociosa separada).

- **RAID 5E**: espaço reservado bloqueado ao final de cada unidade
- **RAID 5EE**: espaço reservado integrado ao arranjo como blocos adicionais → **reconstrução mais rápida**

Ambos: mín. 4 unidades; capacidade = (n−2) × Cmin; sem compartilhamento de hot spare entre arranjos.

### RAID 50
RAID 0 sobre múltiplos grupos RAID 5. Mín. 6 unidades (3 por grupo). Tolera 1 falha por grupo RAID 5.

### RAID 6
Dupla paridade (XOR + Reed-Solomon). Tolera **2 falhas simultâneas** por arranjo. Penalidade: 2 unidades por paridade (vs. 1 do RAID 5). Hot spare suportado.

### RAID 6E
Versão do RAID 5E para RAID 6: hot spare distribuído + dupla paridade. Capacidade = (n−3) × Cmin.

### RAID 60
RAID 0 sobre múltiplos grupos RAID 6. Mín. 8 unidades (4 por grupo). Tolera 2 falhas por grupo.

### JBOD
Just a Bunch of Disks. Não é RAID. Concatenação de unidades de capacidades diferentes em uma única lógica. Capacidade = soma total. Sem redundância. Disponível em controladoras RAID.

> [!warning] JBOD vs. RAID 0
> RAID 0 nível as unidades pela menor → perda de espaço. JBOD preserva capacidade total — indicado para unidades de capacidades diferentes.

---

## RAID: Software vs. Hardware

| Tipo | Controlador | Desempenho | Custo |
|---|---|---|---|
| **Software** | CPU da máquina (via placa-mãe) | Menor; consome CPU | Sem custo adicional |
| **Hardware** | Chip dedicado (placa de expansão PCIe) | Maior; CPU liberada | Custo da placa controladora |
| **Híbrido** | Chip parcial + CPU | Intermediário | Intermediário |

> [!warning]
> Nem toda placa de expansão RAID é por hardware. Verificar especificações antes de comprar.

---

## Paridade: Algoritmos

| Algoritmo | Modos | Nível de dados |
|---|---|---|
| Código de Hamming (ECC) | RAID 2 | Bit |
| XOR | RAID 3, 4, 5, 6 (1ª paridade) | Byte / bloco |
| Reed-Solomon | RAID 6 (2ª paridade) | Bloco |

---

## Relações (SPO)

- RAID 0 → aumenta → desempenho; sem redundância; 1 falha = perda total
- RAID 1 → aumenta → confiabilidade; capacidade = 1 unidade
- RAID 5 → combina → striping + paridade distribuída; 1 falha tolerada; mais eficiente em espaço que RAID 10
- RAID 6 → estende → RAID 5 com dupla paridade; 2 falhas toleradas
- RAID 10 → é → mais confiável e reconstrução mais rápida que RAID 0+1
- Paridade → é → mais eficiente em espaço que espelhamento com mesma qtd. de unidades
- Hot spare → automatiza → substituição e reconstrução após falha
- JBOD → concatena → unidades de capacidades diferentes sem redundância

---

## Ver também

- [[Disco Rígido]] — unidades mecânicas usadas em arranjos RAID
- [[SSD]] — SSDs também utilizáveis em RAID; RAIN interno (RAID entre chips)
- [[Memória de Massa]] — RAID como camada de confiabilidade/desempenho sobre armazenamento
- [[Portas de Comunicação]] — SATA e SAS como interfaces das unidades no arranjo
