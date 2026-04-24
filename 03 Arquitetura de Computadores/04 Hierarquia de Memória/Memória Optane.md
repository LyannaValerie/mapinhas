---
title: Memória Optane
aliases:
  - Optane
  - 3D XPoint
  - 3D Xpoint
  - PMEM
  - PCM
  - Phase-Change Memory
  - mudança de fase
tags:
  - computação/hardware
  - computação/arquitetura
date: 2026-04-12
source: "Livro Hardware clubedohardware.com.br — Cap. 36"
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Memória Optane (3D XPoint)

> [!warning] Tecnologia descontinuada
> Intel abandonou Optane em **julho de 2022** após 7 anos sem sucesso comercial. Micron encerrou atividades 3D Xpoint em **2021**. Produtos ainda no mercado em período de transição. Caso de estudo: tecnologia superior à concorrente pode falhar por monopólio de fabricação e sensibilidade de preço do consumidor.

**3D XPoint** = nome técnico original (Micron + Intel).  
**Optane** = nome comercial Intel.  
Tipo: **não-volátil**, endereçável individualmente por célula.

→ Concorre com NAND como armazenamento: [[Memória Flash]]  
→ Concorre com DDR como memória principal: [[RAM — Tecnologias e Módulos]]  
→ Posição na hierarquia: [[Hierarquia de Memória]] (entre L4-DRAM e L5-NAND)

---

## Relações (SPO)

- Optane → usa → tecnologia PCM (mudança de fase)
- PCM → altera → estado cristalino ↔ amorfo do material
- Estado cristalino → representa → "0" (baixa resistência, alta refletividade)
- Estado amorfo → representa → "1" (alta resistência, baixa refletividade)
- Optane → não requer → transistor por célula (seletor basta)
- Optane → permite → endereçamento individual de célula (como NOR/RAM)
- PMEM modo memória → usa → DRAM como cache L4 para Optane
- PMEM modo direto → expõe → Optane como bloco não-volátil persistente

---

## Arquitetura — PCM (Phase-Change Memory)

Princípio demonstrado por **Gordon Moore** (cofundador da Intel) em **1970**.  
Transferida para Numonyx (Intel + STMicroelectronics, 2008) → comprada pela Micron (2010).

### Célula de armazenamento

Estrutura: **seletor + célula de memória** (sem transistor dedicado por célula).

```
[Seletor]
    ↓
[Célula de material de mudança de fase]
```

- Tensão aplicada no seletor → leitura ou escrita da célula
- **Sem transistor por célula** → maior densidade que NAND (transistor+capacitor ou transistor+porta flutuante)

### Princípio de leitura/escrita

| Estado | Estrutura | Resistência | Refletividade | Bit |
|---|---|---|---|---|
| Cristalino | Ordenado | Baixa | Alta | 0 |
| Amorfo | Desordenado | Alta | Baixa | 1 |

Calor controlado muda o estado do material → não-volátil (estado persiste sem alimentação).

---

## Optane como Armazenamento

Vantagens sobre [[Memória Flash]] NAND (dados Intel):

| Critério | Optane vs NAND |
|---|---|
| Latência | **1.000× menor** |
| Vida útil | **1.000× maior** |
| Endereçamento | Por **célula individual** (como NOR/RAM) |
| Densidade | — (inferior à NAND, mas superior à DRAM em 10×) |
| Custo | Muito superior (monopólio Intel) |

### Produtos de armazenamento

1. **SSD Optane puro**: maior desempenho e vida útil; custo proibitivo para usuário final
2. **SSD cache Optane**: pequena capacidade Optane como cache entre computador e HDD/SSD principal
3. **SSD híbrido Optane+NAND QLC**: pequena porção Optane acelera acesso aos chips NAND QLC principais

> Intel encerrou SSDs Optane para usuário final em **2021**; manteve apenas DC (Data Center) onde vida útil justifica custo — especialmente servidores com alta taxa de escrita diária.

---

## Optane como RAM — PMEM

Módulos **DIMM** com chips Optane para servidores (**Optane PMEM — Persistent Memory**).  
Densidade muito superior à DRAM → capacidades colossais de memória por servidor.

### Modos de operação

| Modo | DRAM | Optane PMEM | SO/apps |
|---|---|---|---|
| **Memória** | Cache L4 para Optane | Opera como RAM volátil | Compatibilidade total; sem modificações |
| **App Direct** | Memória principal (RAM) | Bloco não-volátil persistente (memória ou armazenamento de arquivos) | Requer SO e apps compatíveis |

> Ambos os modos podem coexistir simultaneamente.  
> Processador do servidor precisa suportar PMEM explicitamente.

---

## Por que Falhou

1. **Monopólio**: apenas Intel fabricava → sem concorrência de preços → custo elevado
2. **Sensibilidade de preço**: usuário final não paga premium por desempenho que mal percebe
3. **Desinformação**: maioria dos usuários e técnicos desconhecia a existência da tecnologia
4. **Micron saiu**: 2021 → Intel ficou sozinha, insustentável
5. **Flash NAND** suficientemente boa para a maioria dos casos de uso

---

## Posição na Hierarquia de Memória

[[Hierarquia de Memória]] não inclui Optane, mas ela se encaixaria entre L4 e L5:

```
L4  DRAM (volátil, rápida, cara/byte)
L4+ Optane PMEM (não-volátil, mais lenta que DRAM, mais rápida que NAND, muito mais densa)
L5  NAND Flash (não-volátil, lenta, barata/byte)
```

---

## Conexões com Outras Notas

- [[Memória Flash]] — Optane compete com NAND; endereçamento por célula vs. por bloco
- [[RAM — Tecnologias e Módulos]] — PMEM usa slot DIMM; modo memória = DRAM como cache L4
- [[Hierarquia de Memória]] — nova camada entre DRAM (L4) e NAND (L5)
- [[ROM — Tipos]] — Optane é não-volátil como ROM, mas leitura/escrita como RAM
- [[Memória]] — não-volátil com acesso aleatório: mistura propriedades de RAM e ROM
