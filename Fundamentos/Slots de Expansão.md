---
title: Slots de Expansão
aliases:
  - PCI Express
  - PCIe
  - M.2
  - Mini PCIe
  - mSATA
  - NGFF
  - slots PCIe
  - pistas PCIe
tags:
  - computação/fundamentos
  - computação/hardware
date: 2026-04-12
source: "Livro Hardware clubedohardware.com.br — Cap. 38"
---

# Slots de Expansão

Conectores na [[Placa-mãe]] que permitem instalar placas de expansão, adicionando funcionalidades ausentes na placa-mãe. Existem desde o primeiro PC.

## Relações (SPO)

- Slots de expansão → localizados em → [[Placa-mãe]]
- PCIe → é → conexão ponto a ponto em série (não barramento — ver [[Barramento]])
- PCIe → agrega → pistas (lanes) em grupos x1/x4/x8/x16
- Versão PCIe → determina → largura de banda por pista
- Slot PCIe → tem → formato físico independente da conexão lógica
- Versão PCIe do slot → determinada por → controlador no processador ou chipset
- M.2 → sucede → Mini PCIe e mSATA
- Slot M.2 configuração B → aceita → PCIe x2, SATA, USB, áudio
- Slot M.2 configuração M → aceita → PCIe x4, SATA

---

## PCI Express (PCIe)

**Conexão ponto a ponto em série.** Abreviação oficial: **PCIe** (não "PCI-E").  
Protocolo PCIe também disponível em: USB4, Thunderbolt, SATA Express, SAS.

> [!warning] PCI ≠ PCIe
> PCI e PCI-X eram **barramentos** com comunicação paralela. PCIe é **conexão ponto a ponto** com comunicação em série — completamente diferentes. "Barramento PCI Express" é equívoco.

### Pistas (Lanes)

Cada pista = conexão **simplex dupla** (transmissão e recepção em caminhos separados), usando transmissão diferencial em série.  
`x` = número de pistas. Largura de banda total = largura de banda x1 × número de pistas.  
Números mais comuns: **x1, x4, x8, x16**.

### Versões

| Revisão | Codificação | Clock efetivo | x1 | x4 | x8 | x16 |
|---|---|---|---|---|---|---|
| 1.0 | 8b/10b | 2,5 GT/s | 250 MB/s | 1 GB/s | 2 GB/s | 4 GB/s |
| 2.0 | 8b/10b | 5 GT/s | 500 MB/s | 2 GB/s | 4 GB/s | 8 GB/s |
| 3.0 | 128b/130b | 8 GT/s | 1 GB/s | 4 GB/s | 8 GB/s | 16 GB/s |
| 4.0 | 128b/130b | 16 GT/s | 2 GB/s | 8 GB/s | 16 GB/s | 32 GB/s |
| 5.0 | 128b/130b | 32 GT/s | 4 GB/s | 16 GB/s | 32 GB/s | 64 GB/s |
| 6.0 | PAM4/FLIT | 64 GT/s | 8 GB/s | 32 GB/s | 64 GB/s | 128 GB/s |
| 7.0 | PAM4/FLIT | 128 GT/s | 16 GB/s | 64 GB/s | 128 GB/s | 256 GB/s |

> Larguras de banda por sentido (transmissão **ou** recepção). A PCI-SIG divulga o dobro (soma os dois sentidos).  
> Versões 6.0 e 7.0 sem suporte em processadores/chipsets no momento de publicação (2022).  
> Valores 3.0+ ligeiramente arredondados para cima.

---

## Slots PCIe — Formato Físico vs. Conexão Lógica

Formato físico e conexão lógica são **independentes**:

- Slot x16 pode operar logicamente a x8 ou x4
- Número de contatos visíveis indica pistas reais: metade = x8, 1/4 = x4 (convenção não universal — consultar manual)
- Versão PCIe do slot = versão do controlador ao qual está conectado (processador ou chipset) — não visível externamente
- Processador e chipset **podem ter versões diferentes** (ex: CPU PCIe 4.0 + chipset PCIe 3.0)

### Compatibilidade entre placas e slots

| Situação | Resultado |
|---|---|
| Placa menor em slot maior | Funciona; slot traseiro deve ser aberto (open-ended) |
| Versões diferentes | Funciona; largura de banda = versão menor |
| Dois slots x16 com 16 pistas no controlador | Cada slot opera a x8 quando ambos ocupados |

> Verificar manual da placa-mãe para configuração real (pistas e versão) de cada slot.

---

## Formatos Legados (Obsoletos)

> [!note] Substituídos pelo M.2
> Mini PCIe e mSATA não aparecem em máquinas novas. Relevantes para manutenção de hardware mais antigo.

| Slot | Conexão | Aparência física | Uso típico |
|---|---|---|---|
| **Mini PCIe** | PCIe x1 (2.0) + USB 2.0 | Idêntica ao mSATA | Wi-Fi/Bluetooth, SSD, HDD |
| **mSATA** | SATA (apenas) | Idêntica ao Mini PCIe | SSD e HDD SATA |

> Mini PCIe e mSATA têm conector físico idêntico — verificar especificação antes de instalar.  
> mSATA conectado em paralelo a porta SATA da placa-mãe: os dois **não podem ser usados simultaneamente**.

---

## M.2 (NGFF)

Sucessor do Mini PCIe e mSATA. Presente em desktops e portáteis.  
Periféricos mais comuns: **SSDs** e **placas Wi-Fi/Bluetooth**.

### Tamanhos — Formato LLCC

`LL` = largura (mm) · `CC` = comprimento (mm).

| Dimensão | Valores possíveis |
|---|---|
| Largura | 12, 16, 20, **22** mm |
| Comprimento | 16, 26, 30, 38, 42, **60**, **80**, 110 mm |

Mais comuns: **2230** (Wi-Fi/Bluetooth) · **2280** (SSD).  
Slot aceita múltiplos comprimentos via porca de fixação em orifícios distintos.

### Configurações de Chave (Key)

| Configuração | Conexões disponíveis | PCIe máximo |
|---|---|---|
| **B** | PCIe x2, SATA, USB, áudio | x2 |
| **M** | PCIe x4, SATA | x4 |

Determinadas pela posição do chanfrado (slot) e ranhura (placa). Placas B+M aceitam ambos os tipos de slot.

### Considerações Práticas

- Versão PCIe depende do controlador ao qual o slot M.2 está conectado (processador ou chipset)
- AMD Zen: slots M.2 podem conectar diretamente ao processador
- Intel: slots M.2 para Wi-Fi podem usar **CNVi** (proprietário) — pode não suportar PCIe/SATA convencional
- Placas-mãe de entrada podem omitir PCIe no M.2 → incompatível com SSDs NVMe e placas Wi-Fi
- **Sempre consultar o manual** — cada slot M.2 pode ter conexões diferentes

---

## Ver também

- [[Placa-mãe]] — diagrama CPU↔chipset; pistas PCIe diretas (CPU) vs. via chipset; CNVi Intel
- [[Barramento]] — PCIe é ponto a ponto, não barramento; PCI/PCI-X eram barramentos
- [[Memória Flash]] — SSDs NVMe usam PCIe via M.2 ou slot direto
- [[Hierarquia de Memória]] — SSDs NVMe (L5) acessados via PCIe
