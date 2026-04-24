---
title: Circuito Regulador de Tensão
aliases:
  - VRM
  - Voltage Regulator Module
  - controlador PWM
  - PWM
  - fase VRM
  - DrMOS
  - VID
  - VCORE
  - VCC
tags:
  - computação/fundamentos
  - computação/hardware
date: 2026-04-12
source: "Livro Hardware clubedohardware.com.br — Cap. 40"
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Hardware]]

# Circuito Regulador de Tensão (VRM)

Converte tensões da fonte de alimentação nas tensões requeridas pelo processador, memória, chipset e demais circuitos da [[Placa-mãe]].

## Relações (SPO)

- VRM → converte → +12 V (ATX12V/EPS12V) nas tensões do processador (VCORE/VCC)
- Processador → informa ao controlador PWM → tensão necessária via código VID (8 bits)
- Controlador PWM → controla → ativação/desativação de fases
- Cada fase → carrega/descarrega → indutor para produzir tensão de saída
- Mais fases → menor ripple → tensão de saída mais limpa
- Maior frequência de chaveamento → maior eficiência → menor ripple
- DrMOS → substitui → 3 transistores (driver + lado alto + lado baixo) em 1 CI
- Dobrador de fase → dobra número de fases → reduz frequência de chaveamento pela metade

---

## Funcionamento

Conversor **DC-DC** (fonte chaveada): pega tensão da fonte → converte em forma de onda retangular de alta frequência → converte em tensão contínua de menor valor.

### VID — Voltage Identifier

Processador informa a tensão necessária ao controlador PWM via código de **8 bits** (256 valores possíveis), transmitido em série:
- **VR** (Intel) / **SVI** (AMD): tabela publicada pelo fabricante mapeando cada código VID → tensão exata
- Exemplo: código `01001101` → 1,13125 V

### Tensões geradas

| Tensão | Intel | AMD | Gerada por |
|---|---|---|---|
| Principal | VCC | VCORE | VRM principal |
| Controlador de memória | — | Ponte Norte | VRM secundário |
| iGPU / PCIe / outros | variável | variável | VRM adicional |

VRM principal: localizado entre soquete do processador e painel traseiro.  
Outros VRMs (menores): memória, chipset, tensões auxiliares.  
ATX12VO: VRM na placa-mãe também gera +5 V a partir de +12 V para dispositivos SATA.

---

## Arquitetura

```
PROCESSADOR
    │ VID (8 bits, série)
    ▼
CONTROLADOR PWM ──── FASE 1
                ├─── FASE 2
                └─── FASE n
                         │
                    TENSÃO DE SAÍDA (VCORE/VCC)
```

### Fases

Cada fase: indutor + capacitores + 3 MOSFETs (driver, lado alto, lado baixo).  
Fases operam **defasadas** entre si → ripples se cancelam parcialmente.

**Trade-off número de fases:**

| Fases | Ripple | Qualidade da tensão |
|---|---|---|
| Poucas | Alto | Pior |
| Muitas | Baixo | Melhor |

**Trade-off frequência de chaveamento:**

| Frequência | Eficiência | Ripple |
|---|---|---|
| Baixa (ex: 200 kHz) | Menor | Maior |
| Alta (ex: 1 MHz) | Maior | Menor |

### Dobradores de Fase

Quando controlador PWM não suporta o número de fases desejado:

```
Controlador PWM (N fases) → N dobradores → 2N fases
Frequência por fase = frequência PWM ÷ 2
```

Exemplo: controlador 8 fases + 8 dobradores = 16 fases, cada fase a metade da frequência original.

### Formato de Divulgação

`a+b` ou `a+b+c` onde:
- `a` = fases para VCORE/VCC (tensão principal do processador)
- `b` = fases para tensão secundária (controlador de memória / ponte norte AMD)
- `c` = fases para tensão terciária (ex: iGPU)

Exemplo: **16+4** = 16 fases VCORE + 4 fases tensão secundária; melhor que **8+2**.  
Contar indutores = contar fases totais (a+b+c). Multímetro na escala de continuidade identifica quais estão interconectados.

---

## Componentes

### Controlador PWM

Monitoramento da saída por laço de realimentação → corrige tensão se divergir do VID.

| Tipo de monitoramento | Velocidade de correção | Precisão |
|---|---|---|
| **Analógico** | Mais rápido (sem conversão A/D) | Menor |
| **Digital** | Mais lento (conversão A/D) | Maior |

> "Híbrido": termo de marketing; na prática são analógicos.

### Transistores (MOSFETs)

Por fase: driver + lado alto + lado baixo.  
Alternativas (ordem crescente de qualidade):
1. 3 transistores discretos
2. CI driver (substitui apenas o transistor driver)
3. **DrMOS**: CI único com os 3 transistores — maior eficiência, menor calor, maior frequência possível

Parâmetro-chave: **RDS(on)** — resistência dreno-fonte quando conduzindo (em mΩ).  
Menor RDS(on) = menor perda resistiva = maior eficiência = menos calor.

### Indutores (Bobinas / Chokes)

1 indutor por fase. Núcleos comparados:

| Núcleo | Saturação magnética | Perda (histerese) | Custo |
|---|---|---|---|
| **Ferro** | Menor | Maior | Menor |
| **Ferrite** | Maior | Menor | Maior |

Ferrite preferível: menor perda → menos calor → maior eficiência.  
Aspecto visual: bobina de ferro (núcleo exposto) vs. bobina de ferrite (encapsulada).

### Capacitores

| Tipo | Problema | Qualidade |
|---|---|---|
| Eletrolítico tradicional | Pode vazar/estufar/explodir | Pior |
| Eletrolítico japonês (Chemi-Con, Rubycon, Matsushita, Hitachi, Sanyo) | Raro; mais resistente | Melhor que genérico |
| **Sólido / polímero** | Nenhum (sem eletrólito líquido) | Bom |
| **Tântalo / Hi-c / polímero altamente condutivo** | Nenhum (SMT) | Melhor |

---

## Impacto Prático

- VRM melhor → tensão mais limpa → overclock mais estável e maior
- VRM mais eficiente → menos calor na placa-mãe → menor temperatura geral
- VRM com mais fases = melhor qualidade de tensão para processadores de alto TDP
- Comparar placas-mãe com mesmo chipset: VRM é diferencial principal

---

## Ver também

- [[Placa-mãe]] — VRM como componente; conectores ATX12V/EPS12V que o alimentam
- [[Portas de Comunicação]] — ATX12VO: VRM na placa-mãe gera +5 V para SATA a partir de +12 V
- [[Processador]] — VID, VCORE/VCC, tensões auxiliares (controlador de memória, iGPU, PCIe)
