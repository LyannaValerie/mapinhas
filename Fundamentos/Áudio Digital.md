---
title: Áudio Digital
aliases:
  - placa de som
  - codec de áudio
  - ADC
  - DAC
  - PCM
  - LPCM
  - taxa de amostragem
  - resolução de áudio
  - ruído de quantização
  - SNR
  - surround
  - SPDIF
  - Toslink
  - MIDI
  - sintetizador
  - wave table
  - síntese FM
  - HSP
  - áudio on-board
tags:
  - computação/fundamentos
  - computação/hardware
date: 2026-04-12
source: "Livro Hardware clubedohardware.com.br — Cap. 51"
---

# Áudio Digital

Som = sinal analógico. Computador = sistema digital. Conversão necessária via **ADC** (Analog-to-Digital Converter) e **DAC** (Digital-to-Analog Converter).

**Formas de gerar som no computador:**

| Técnica | Como | Uso |
|---|---|---|
| Bit 0/1 direto ao speaker | Varia duração de 0 e 1 | Bipes da placa-mãe; speaker interno |
| Conversão ADC/DAC (PCM) | Digitaliza forma de onda analógica | Vídeos, mp3, wav, streaming, CD, DVD, Blu-ray |
| Sintetizador | Recebe notas + instrumento → gera som | Arquivos MIDI (.mid), trilhas de jogos |

---

## Princípios de Áudio Digital (PCM)

PCM = Pulse-Code Modulation (modulação por código de pulso). Processo de amostragem da forma de onda analógica.

### Taxa de Amostragem

Número de amostras por segundo (Hz). Define resolução no **eixo X** (tempo).

**Teorema de Nyquist-Shannon:** taxa de amostragem ≥ 2× frequência máxima a capturar.

| Taxa | Frequência máxima captada | Uso |
|---|---|---|
| 44.100 Hz | 22.050 Hz (≥ 20 kHz audível) | **CD** — padrão mínimo de boa qualidade |
| 48.000 Hz | 24.000 Hz | Áudio profissional básico |
| 96.000 Hz | 48.000 Hz | Estúdio profissional |
| 192.000 Hz | 96.000 Hz | Estúdio profissional |

Taxa menor → frequências altas cortadas → som "comprimido".

### Resolução (bits)

Número de bits por amostra. Define resolução no **eixo Y** (amplitude). Determina quantos níveis distintos de amplitude são possíveis (2ⁿ).

| Resolução | Níveis | Uso |
|---|---|---|
| 8 bits | 256 | Baixa qualidade |
| 16 bits | 65.536 | **CD** — padrão mínimo de boa qualidade |
| 20 bits | 1.048.576 | Profissional |
| 24 bits | 16.777.216 | Estúdio, DVD, Blu-ray |

Resolução baixa → pontos próximos arredondados para o mesmo valor → **ruído de quantização**.

### Relação Sinal-Ruído (SNR)

$$SNR = (6{,}02 \cdot n) + 1{,}76 \text{ dB}$$

onde n = bits de resolução. Maior = melhor.

| Resolução | SNR teórico |
|---|---|
| 8 bits | 49,6 dB |
| 16 bits | 98,1 dB |
| 20 bits | 122,2 dB |
| 24 bits | 146,2 dB |

> [!warning] SNR real vs. teórico
> Codec físico insere **ruído branco** (interferência eletromagnética dos outros componentes da placa-mãe), limitando o SNR real abaixo do teórico. SNR mínimo recomendado para gravação profissional: **100 dB**.  
> Codec apresenta dois valores: **ADC SNR** (entrada analógica) e **DAC SNR** (saída analógica).  
> Microfone USB tem ADC próprio — SNR determinado pelo microfone, não pelo codec.

### Canais

Cada canal = conversão ADC/DAC independente. Configurações:

| Configuração | Canais de áudio | Canal subwoofer | Total |
|---|---|---|---|
| Mono | 1 | 0 | 1 |
| Estéreo | 2 | 0 | 2 |
| 4.0 | 4 | 0 | 4 |
| **5.1** | 5 | 1 | 6 |
| **7.1** | 7 | 1 | 8 |
| 5.2 | 5 | 2 | 7 |

Número antes do ponto = canais de áudio. Número após o ponto = canais de subwoofer. Som surround = também chamado "áudio 3D" ou "áudio posicional".

### Tamanho do Arquivo (sem compressão)

$$\text{Tamanho (bytes)} = \frac{t \cdot S \cdot r \cdot c}{8}$$

Onde: t = segundos, S = taxa amostragem (Hz), r = resolução (bits), c = canais.

**Regra prática:** 1 minuto de áudio CD (44.100 Hz, 16 bits, estéreo) ≈ **10 MiB**.

### Compressão

| Tipo | Formato | Qualidade | Tamanho |
|---|---|---|---|
| Sem compressão | WAV, PCM, LPCM | Máxima | Grande |
| Com perda | MP3, streaming, DVD, Blu-ray | Depende do nível | Menor |

Compressão com perda = qualidade degradada em proporção ao nível de compressão. Analogia: JPEG em imagens.

---

## Sintetizador

Reproduz arquivos MIDI sem instrumentos físicos. Recebe nota + instrumento → gera som.

| Técnica | Como | Qualidade | Status |
|---|---|---|---|
| **Síntese FM** | Cria sons eletronicamente (Yamaha OPL3) | Som artificial — "computador antigo" | Obsoleta |
| **Wave table** | Reproduz sons gravados em estúdio; 128 instrumentos General MIDI | Som real | Atual |

**Atualmente:** wave table por software — processador da máquina; sons em arquivos instalados pelo driver. Antigamente: chip hardware na placa de som + ROM com sons.

---

## Implementações

### Áudio On-Board (HSP)
Todas as placas-mãe modernas incluem áudio integrado. Modelo **HSP** (Host Signal Processing): processador da máquina faz todo o processamento.

Componente central: **codec** (COdificador/DECodificador) — chip com ADC + DAC + interface SPDIF. Conectado ao chipset ou processador via interface digital.

### Placas-Mãe Topo de Linha
Controlador de áudio dedicado (ex: Creative) + codec. Processamento não usa CPU. Melhorias para SNR: capacitores de melhor qualidade, isolamento elétrico da seção de áudio, blindagem eletromagnética.

### Placa de Som Discreta
Controlador de áudio + codec dedicados (não usa CPU). Indicada para:
- SNR garantido ≥ 100 dB (gravação profissional)
- Interface SPDIF de entrada (rara em placas-mãe)
- Interface MIDI
- Liberar CPU de processamento de áudio

> [!note] Nomenclatura
> "Off-board" para placa de som discreta = inglês inventado no Brasil. Correto: **placa de som discreta** ou **avulsa**.

---

## Conectores Analógicos (P2 / 3,5 mm)

> "P2" = apelido brasileiro. Nome correto: **conector 3,5 mm estéreo**.

| Cor | Função |
|---|---|
| Rosa | Mic in (entrada de microfone) |
| Azul | Line in (entrada de linha) |
| Verde | Alto-falantes frontais |
| Preta | Alto-falantes traseiros |
| Laranja | Alto-falante central + subwoofer |
| Cinza | Surround central |

**Saídas compartilhadas:** mic in e line in assumem funções de conectores ausentes → não é possível usar microfone/line in e sistema surround simultaneamente.  
**Saídas independentes:** preferir se usar sistema surround analógico + microfone simultaneamente.

Configuração dos conectores ajustável por software (painel de controle do driver de áudio).

> [!note] Terminologia correta
> Conector para fones = **saída** (sinal sai do computador). "Entrada para fones" é incorreto. Usar "conector" quando em dúvida.

---

## Interface SPDIF

Sony/Philips Digital Interconnect Format. Transmissão digital de áudio — sem degradação analógica.

| Tipo | Conector |
|---|---|
| Coaxial | RCA |
| Óptico | **Toslink** |

- Presente no painel traseiro de muitas placas-mãe
- Pinos internos: rotulados `SPDIF_OUT`, `SPDIFO`, `HDMI_SPDIF`, `JSP1` ou similar
- Maioria das placas: **apenas saída**; entrada SPDIF requer placa discreta
- Alternativa ao HDMI para enviar áudio digital a receiver/home theater sem vídeo

---

## Interface MIDI

Musical Instrument Digital Interface. Transmite **notas musicais** (ativação/desativação), **não áudio**. Permite sincronização e controle remoto de instrumentos eletrônicos.

**Dois usos do computador com MIDI:**
1. **Sequenciador** — define linha do tempo de notas/instrumentos; controla múltiplos instrumentos via MIDI Out
2. **Sintetizador** — wave table por software recebe comandos MIDI → áudio sai pela placa de som

Conector: **DIN 5 pinos** (MIDI In + MIDI Out). Disponível via:
- Dispositivo USB externo (formato atual)
- Placa de som discreta com interface MIDI integrada

> Arquivos `.mid` contêm apenas sequência de notas — sem áudio gravado → tamanho de arquivo muito pequeno.

---

## Relações (SPO)

- ADC → converte → sinal analógico para binário (gravação/captura)
- DAC → converte → binário para sinal analógico (reprodução)
- Taxa de amostragem → define → resolução temporal (eixo X); mínimo = 2× f_máxima (Nyquist)
- Resolução em bits → define → resolução de amplitude (eixo Y); maior = menor ruído de quantização
- SNR → aumenta → 6,02 dB por bit adicional
- Compressão com perda → reduz → tamanho; degrada qualidade proporcionalmente
- Wave table → reproduz → sons gravados em estúdio; qualidade superior ao FM
- HSP → usa → CPU da máquina para processamento de áudio
- SPDIF → transmite → áudio digital sem degradação analógica
- MIDI → transmite → notas (não áudio); permite controle/sincronização de instrumentos

---

## Ver também

- [[Portas de Comunicação]] — SPDIF (coaxial RCA, óptico Toslink), conectores P2 na placa-mãe
- [[Placa-mãe]] — chipset controla codec; conector SPDIF_OUT interno; áudio on-board
- [[Conectores de Vídeo]] — HDMI como alternativa para áudio digital sem cabo adicional
