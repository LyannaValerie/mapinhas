---
title: Fabricação de Chips
tags:
  - hardware
  - semicondutores
  - fabricação
aliases:
  - wafer
  - fotolitografia
  - processo de fabricação
  - encapsulamento
  - nanômetros
  - TSMC
  - die
  - transistor MOSFET
---

# Fabricação de Chips

Processo usado para qualquer circuito integrado — processadores, GPUs, memória etc.

---

## Fluxo Geral de Fabricação

```
Projeto → Wafer → Fotolitografia → Teste/Corte → Encapsulamento → Teste → Separação → Rotulagem → Empacotamento
```

| Etapa | Detalhe |
|-------|---------|
| **Projeto** | Engenheiros projetam o chip; geram máscaras (plantas para fotolitografia) |
| **Wafer** | Substrato de silício puro onde chips são fabricados |
| **Fotolitografia** | Cria transistores e conexões no wafer via luz UV + máscaras |
| **Teste 1** | Chips testados ainda no wafer; defeituosos descartados |
| **Corte** | Chips cortados do wafer (pastilha = **die**) |
| **Encapsulamento** | Invólucro + terminais adicionados ao die |
| **Teste 2** | Chip encapsulado testado |
| **Separação** | Chips agrupados por clock máximo atingido → define SKU/modelo |
| **Rotulagem** | Marcação laser: modelo, revisão, lote |
| **Empacotamento** | Varejo (caixa) ou OEM (bandeja) |

> [!note] Binning
> Chips do mesmo wafer saem com clocks máximos diferentes. O teste final determina o SKU — chips mais rápidos → produto premium; mais lentos → produto de entrada (ou descartados).

---

## Fabricação do Wafer de Silício

- Silício extraído como SiO₂ da areia → purificado pelo **método Czochralski**
- Processo Czochralski: cristal-semente mergulhado em silício fundido + girado enquanto suspenso → forma **lingote** (ingot) cilíndrico
- Pureza: **99,9999%** (silício monocristalino)
- Lingote: 1–2 m comprimento, ~100 kg, até **300 mm de diâmetro**
- Lingote fatiado → wafers polidos → substrato "virgem"

> [!question] Por que o wafer é redondo?
> O processo Czochralski gira o silício → forma cilíndrica natural. Não há como obter lingote quadrado por esse método.

Wafer maior → mais chips por wafer → **menor custo por chip**.

---

## Processo de Fotolitografia

Três grandes etapas: **fotolitografia** → **ionização** → **camadas de metal**.

Tudo ocorre em **sala limpa** automatizada. Uma partícula de poeira destrói o chip.

### 1. Fotolitografia (criação das camadas de silício)

| Passo | Descrição |
|-------|-----------|
| **Exposição** | Wafer coberto de substância fotossensível (solúvel à UV); máscara aplicada; UV expõe áreas a remover |
| **Lavagem** | Solvente remove parte "mole" (exposta ao UV) |
| **Etching** | Produtos químicos removem silício exposto → baixo relevo seguindo padrão da máscara |
| **Remoção do fotorresistente** | Parte "dura" (protegida pela máscara) removida |
| **Nova camada** | Silício aplicado preenchendo lacunas ou adicionando camada nova |

Processo repetido **dezenas de vezes** em processadores modernos, intercalado com ionização.

### 2. Ionização (dopagem)

- Fotorresistente define áreas a **não** ionizar
- Áreas expostas recebem bombardeamento de íons → altera condutividade elétrica
- Cria semicondutores **tipo N** (elétrons livres) ou **tipo P** (lacunas)

| Tipo | Dopantes usados |
|------|----------------|
| **N** (carga negativa) | Fósforo, antimônio, arsênico |
| **P** (carga positiva) | Bromo, índio, gálio |

### 3. Camadas de metal (cobre)

1. Camada isolante aplicada com **buracos** para terminais dos transistores
2. **Galvanização**: wafer em solução de sulfato de cobre + tensão elétrica → íons de cobre preenchem buracos
3. **Polimento**: cobre superficial removido → só o cobre nos buracos permanece → transistores completos
4. Conexões de metal entre transistores (múltiplas camadas) → funcionam como "fios internos"

---

## Tamanho do Processo de Fabricação (nm)

### O que o número **não** significa (mito comum)

> [!warning] O número em nm ≠ comprimento do gate do transistor
> - Até ~1990: o número indicava o comprimento do gate do transistor MOSFET (o terminal que liga/desliga o transistor)
> - A partir do **processo 250 nm da Intel (1997)**: Intel rompeu a correlação — gate já tinha 200 nm
> - No **processo 45 nm**: gate atingiu tamanho ideal (~25 nm). No processo 32 nm o gate foi **aumentado**
> - Hoje: o número é apenas um **identificador de geração**, não mede nenhum elemento físico

### O que o número realmente significa

- **"Número de versão" inverso**: menor = mais recente
- Padronizado pela **ITRS** (International Technology Roadmap for Semiconductors)
- Cada fabricante usa processo diferente; "14 nm Intel" ≠ "14 nm TSMC" — mesma geração, tecnologias distintas
- Comparação entre fabricantes: número menor = geração mais avançada

### Vantagens de processo menor

- Mais chips por wafer → **custo menor**
- Mais transistores no mesmo tamanho físico
- Transistores mais próximos → **mais rápido**
- Menos elétrons para ativar transistores → **menor tensão → menos consumo e calor**

### Fabricantes (2022)

| Fabricante | Modelo de negócio |
|-----------|------------------|
| **Intel** | Projeta e fabrica (IDM — Integrated Device Manufacturer) |
| **TSMC** | Só fabrica (foundry) — AMD, NVIDIA, Apple |
| **Samsung** | Projeta e fabrica; também foundry |
| **Global Foundries** | Foundry — surgiu da separação das fábricas da AMD em 2009 |

AMD usa TSMC + Samsung + Global Foundries. NVIDIA usa TSMC + Samsung.

---

## Encapsulamento

Die = frágil + pequeno → precisa de invólucro (plástico, cerâmico ou metálico) + terminais.

Funções do encapsulamento: **proteção mecânica + conexão elétrica + dissipação de calor**.

Die pode ocupar fração pequena da área total encapsulada. Chip encapsulado = **circuito integrado (CI / IC)**.

### Versões: soquete vs. SMD

- **Soquete**: permite remoção/substituição sem soldar
- **SMD** (Surface-Mount Device): soldado diretamente na PCB via **SMT** (Surface-Mount Technology)

Processadores desktop/servidor → soquete (usuário substitui). Processadores mobile → SMD soldado na placa.

### Tipos de encapsulamento

| Tipo | Terminais | Soquete | SMD |
|------|-----------|---------|-----|
| **DIP** (Dual In Parallel) | 2 linhas paralelas | DIP | SOIC, TSOP |
| **Quad** | 4 lados | PLCC | PQFP |
| **Inferior** | Face inferior | PGA (pinos) / LGA (lisos — pinos no soquete) | BGA (esferas) |

> [!tip] LGA vs PGA
> Em LGA os pinos ficam no **soquete** (não no chip). Em PGA os pinos ficam no **chip**. Processadores Intel modernos usam LGA; AMD usa AM5 (LGA) a partir do Ryzen 7000.

---

## Ver também

- [[Processador]] — produto final do processo de fabricação
- [[Gerenciamento de Consumo Elétrico]] — processo menor → menor tensão → menor consumo (P=CV²f)
- [[Desempenho do Processador]] — mais transistores por chip = mais unidades de execução, mais cache
- [[Intel x86]] — histórico de processos de fabricação Intel
