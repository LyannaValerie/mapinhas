---
title: Conectores de Vídeo
aliases:
  - VGA
  - DVI
  - DVI-A
  - DVI-D
  - DVI-I
  - single link
  - dual link
  - HDMI
  - DisplayPort
  - vídeo composto
  - S-Video
  - vídeo componente
  - TMDS
  - HDCP
  - ARC
  - DE15
tags:
  - computação/fundamentos
  - computação/vídeo
  - computação/hardware
date: 2026-04-12
source: "Livro Hardware clubedohardware.com.br — Cap. 45"
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Hardware]]

# Conectores de Vídeo

Conectam [[Placa de Vídeo]] (saída) ao monitor (entrada). Mesma porta em sentidos opostos = entrada ou saída, dependendo do equipamento analisado. Termo neutro correto: **porta** ou **conector**.

> [!tip] Regra geral
> Usar conexão digital sempre que possível. Evita conversões D→A (placa) e A→D (monitor) que degradam imagem. Sinais analógicos não têm correção de erros.

---

## Analógicos (obsoletos)

### Vídeo Composto
Conector **RCA** (amarelo). Resolução máx: 640×480 entrelaçado (480i para não-computador). Sinal de luminância e cor no mesmo fio. Qualidade mais baixa.

### S-Video (Separated Video)
Transmite **luminância** e **crominância** em fios separados. Resolução máx: 480i (igual ao composto). Conectores de 4, 7 ou 9 pinos.

> [!note] S-Video e vídeo componente
> Conector S-Video de **4 pinos** = apenas S-Video. Com **7 ou 9 pinos**: pode ter vídeo componente (verificar manual da placa).

### Vídeo Componente
3× RCA: **Y** (verde, luminância), **Pb/Cb** (azul), **Pr/Cr** (vermelho). Cor dividida em dois fios → qualidade superior ao S-Video. Resolução máx: 1920×1080i/p. Em placas de vídeo: conector S-Video ampliado + adaptador para 3× RCA.

### RGB
Sinal analógico com **cabo separado por canal** (R, G, B + sincronias). Melhor qualidade que VGA por blindagem individual. Padrões:

| Variante | Cabos | Conectores |
|---|---|---|
| RGB5 | R, G, B, Hsync, Vsync | BNC |
| RGB4 | R, G, B, Csync | BNC |
| RGB3 | R, G+Csync, B | BNC |

Monitores RGB antigos usam frequências de varredura diferentes do VGA → incompatíveis diretamente.

### VGA (Video Graphics Array)
Conector **DE15** (15 pinos, D-Sub). Fios independentes: R, G, B, Hsync, Vsync. Sem limite de resolução por hardware — depende do [[Placa de Vídeo#RAMDAC|RAMDAC]] da placa. Melhor conexão analógica disponível para computadores.

> [!info] Nomenclatura
> "VGA" originalmente = resolução 640×480 (IBM). Depois ficou como nome do conector DE15, mesmo para resoluções muito maiores ("Super VGA"). Também confundido com "Video Graphics Adapter" (placa de vídeo).

---

## Híbrido: DVI

**DVI** (Digital Visual Interface) transmite analógico **e/ou** digital no mesmo conector.

### Subtipos

| Tipo | Sinal |
|---|---|
| **DVI-A** | Analógico (mesma qualidade VGA) |
| **DVI-D** | Digital |
| **DVI-I** | Analógico + digital (cabo ou conector) |

Cabos DVI-A e DVI-D só transportam seu respectivo sinal. Cabo DVI-I transporta ambos.

### Single Link vs. Dual Link

| Modo | Pares de dados | Cor máx. | Resolução exemplos |
|---|---|---|---|
| Single link | 3 pares + 1 clock | 24 bits | 1920×1200 @ 60 Hz, 2098×1311 @ 60 Hz |
| Dual link | 6 pares + 1 clock | 24 ou 48 bits | 2560×1600 @ 60 Hz, 1920×1080 @ 144 Hz |

### TMDS (Transition-Minimized Differential Signaling)
Single link usa 4 pares: R, G, B + clock.

- Clock: 25,175–165 MHz
- Taxa de dados: 10× clock → 250–1.650 Mbit/s por par
- Codificação **8b/10b** → largura de banda efetiva: 600–3.960 Mbit/s total (3 pares × dados)
- Clock máx. 165 MHz limita single link a resoluções menores

> [!warning] Cabos dual link falsos
> Conectores com todos os pinos mas pinos extras sem fio. Testar continuidade com multímetro para verificar.

### Conversões DVI

| Conversão | Possível passivamente? | Condição |
|---|---|---|
| DVI-I → VGA | Sim | Placa deve ter sinal analógico disponível |
| DVI ↔ HDMI | Sim | Ambos digitais, mesmo padrão TMDS |
| VGA → DVI (digital) | **Não** | Analógico → digital exige circuito ativo |

> [!warning]
> Muitas placas usam conector DVI-I sem suporte analógico → conversão para VGA impossível mesmo com adaptador passivo. Verificar especificações.

---

## Digital: HDMI

**HDMI** (High-Definition Multimedia Interface). Padrão dominante em eletrônicos de consumo. Transporta vídeo + áudio digital no mesmo cabo. Possui **HDCP** (proteção contra cópia).

### Conectores HDMI

| Tipo | Uso |
|---|---|
| A | Padrão (mais comum) |
| B | Dual link (29 pinos) — não usado na prática |
| C | Mini (portáteis) |
| D | Micro (portáteis) |
| E | Automotivo |

### Versões HDMI

| Versão | BW/canal | Canais | BW máx. | Codificação | Cor máx. | Áudio |
|---|---|---|---|---|---|---|
| 1.0–1.2 | 1,65 Gbit/s | 3 | 4,95 Gbit/s | TMDS | 24 bits | 8 ch, 24 bit, 192 kHz |
| 1.3 | 3,4 Gbit/s | 3 | 10,2 Gbit/s | TMDS | 48 bits | 8 ch, 24 bit, 192 kHz |
| 1.4 | 3,4 Gbit/s | 3 | 10,2 Gbit/s | TMDS | 48 bits | 8 ch, 24 bit, 192 kHz |
| 2.0 | 6 Gbit/s | 3 | 18 Gbit/s | TMDS | 48 bits | 32 ch, 24 bit, 1.536 kHz* |
| 2.1 | 12 Gbit/s | 4 | 48 Gbit/s | 16b/18b | 48 bits | 32 ch, 24 bit, 1.536 kHz* |

*Ex: 8 ch @ 192 kHz ou 16 ch @ 96 kHz.

**ARC** (Audio Return Channel): a partir de 1.4. Permite retorno de áudio TV → receiver/soundbar pelo mesmo cabo HDMI.

### Categorias de Cabo HDMI

| Categoria | Padrão | BW certificado |
|---|---|---|
| 1 (Padrão) | HDMI 1.0–1.2 | até 2,25 Gbit/s (74,25 MHz) |
| 2 (Alta velocidade) | HDMI 1.3/1.4 | até 10,2 Gbit/s (340 MHz) |
| Alta velocidade premium | HDMI 2.0 | até 18 Gbit/s |
| 3 (Ultra alta velocidade, 48G) | HDMI 2.1 | até 48 Gbit/s |

> [!warning] Cabo errado limita desempenho
> Cabo categoria 1 em configuração HDMI 2.0 → largura de banda truncada. Verificar categoria do cabo, não só da porta.

Conversão DVI-D/DVI-I → HDMI: adaptador passivo. Sem sinal de áudio (DVI não tem áudio digital), a menos que haja conexão física adicional.

---

## Digital: DisplayPort

**DisplayPort** voltado a computadores (diferente do HDMI, voltado a eletrônicos de consumo). **Sem royalties** para fabricantes.

Conectores: DisplayPort padrão e **Mini DisplayPort**. A partir da versão 2.0: também **USB Tipo C**.

### Modos DisplayPort

| Modo | BW/canal | BW máx. (4 canais) | Versão mín. |
|---|---|---|---|
| RBR | 1,62 Gbit/s | 6,48 Gbit/s | 1.0 |
| HBR | 2,7 Gbit/s | 10,8 Gbit/s | 1.0 |
| HBR2 | 5,4 Gbit/s | 21,6 Gbit/s | 1.2 |
| HBR3 | 8,1 Gbit/s | 32,4 Gbit/s | 1.3 |
| UHBR 10 | 10 Gbit/s | 40 Gbit/s | 2.0 |
| UHBR 13,5 | 13,5 Gbit/s | 54 Gbit/s | 2.0 |
| UHBR 20 | 20 Gbit/s | 80 Gbit/s | 2.0 |

### Áudio DisplayPort

| Versão | Suporte |
|---|---|
| 1.0–1.1a | 8 ch, 192 kHz, 24 bits |
| 1.2–1.3 | Estéreo 16 bits até 768 kHz **ou** 8 ch até 192 kHz |
| 1.4+ | Até 1.536 kHz totais (8 ch @ 192 kHz, 16 ch @ 96 kHz, 32 ch @ 48 kHz) |

### Multi-Monitor (MST — Multi-Stream Transport)
A partir de **v1.2**: até 3 monitores por porta via **hub DisplayPort** externo ou monitor com hub integrado (daisy-chain). Monitores se conectam em cascata: porta DP → monitor 1 → monitores 2 e 3.

---

## Digital: Thunderbolt

Multiprotocolo: PCIe + DisplayPort + USB num único conector. Detalhes completos em [[Portas de Comunicação#Thunderbolt]].

Versões 1/2: conector **Mini DisplayPort**. Versões 3/4: conector **USB Tipo C**.

---

## Relações (SPO)

- Sinal analógico → sofre → interferência eletromagnética sem correção de erros
- Sinal digital → permite → detecção e correção de erros
- Conexão digital → evita → conversões D/A e A/D desnecessárias
- VGA → usa → conector DE15; resolução máx depende de [[Placa de Vídeo#RAMDAC|RAMDAC]]
- DVI single link → usa → TMDS; clock máx 165 MHz; 24 bits
- DVI dual link → dobra → pares de dados; permite 48 bits ou maior resolução
- DVI-I → converte passivamente para → VGA (se placa tiver sinal analógico)
- DVI ↔ HDMI → conversão passiva → mesmo padrão TMDS digital
- HDMI → inclui → HDCP + ARC + áudio digital no mesmo cabo
- DisplayPort → não exige → royalties; suporta MST (multi-monitor)
- DisplayPort 2.0 → usa → USB Tipo C como conector alternativo
- Thunderbolt → referência → [[Portas de Comunicação#Thunderbolt]]
- Subamostragem de cores → reduz → largura de banda necessária (ver [[Princípios de Vídeo]])

---

## Ver também

- [[Placa de Vídeo]] — RAMDAC, resolução máxima, placa geradora do sinal
- [[Princípios de Vídeo]] — resolução, taxa de atualização, subamostragem, profundidade de cores
- [[Portas de Comunicação]] — Thunderbolt (detalhado), USB Tipo C, DisplayPort via USB4
- [[Placa-mãe]] — localização física dos conectores de vídeo integrado
- [[Monitores de Vídeo]] — entrada do sinal; resolução nativa, G-SYNC/FreeSync (exige DisplayPort)
