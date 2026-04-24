---
title: Modem e DSL
aliases:
  - modem analógico
  - DSL
  - ADSL
  - VDSL
  - G.lite
  - cabo
  - HFC
  - DOCSIS
  - CMTS
  - banda larga
  - dial-up
  - modulação
  - baud rate
tags:
  - computação/hardware
  - computação/redes
date: 2026-04-17
source: "Organização Estruturada de Computadores — Tanenbaum, Cap. 2"
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Hardware]]

# Modem e DSL

Tecnologias de acesso à Internet sobre infraestrutura preexistente — linha telefônica analógica ou rede de TV a cabo.

## Relações (SPO)

- Modem analógico → converte → sinal digital (bits) ↔ sinal analógico (tom de voz)
- Baud rate → mede → símbolos por segundo (≠ bit rate)
- ADSL → divide → linha telefônica em canais de 4 kHz (upstream + downstream assimétricos)
- HFC → combina → fibra óptica (backbone) + cabo coaxial (última milha)
- DOCSIS → define → protocolo de dados sobre rede HFC

---

## Modem Analógico (Dial-up)

A rede telefônica pública (PSTN — *Public Switched Telephone Network*) foi projetada para voz: faixa de **300 Hz a 3400 Hz**. Para transmitir dados digitais sobre ela, é necessário converter bits em tons analógicos.

**Modem** = *Modulador/Demodulador*: converte bits em sinal analógico (modulação) e vice-versa (demodulação).

### Modulação

| Técnica | O que varia | Bits por símbolo |
|---|---|---|
| **ASK** (Amplitude Shift Keying) | Amplitude | 1 |
| **FSK** (Frequency Shift Keying) | Frequência | 1 |
| **PSK** (Phase Shift Keying) | Fase | 1+ |
| **QAM** (Quadrature AM) | Amplitude + Fase | até 8+ |

Modems modernos usam **QAM** — múltiplos bits por símbolo maximizam bit rate dentro da banda disponível.

### Baud Rate vs. Bit Rate

- **Baud rate**: símbolos por segundo (taxa de sinalização)
- **Bit rate**: bits por segundo
- Com 1 bit/símbolo: baud rate = bit rate
- Com 4 bits/símbolo (16-QAM): bit rate = 4 × baud rate

### Padrões V-series (ITU)

| Padrão | Bit rate máx | Notas |
|---|---|---|
| V.34 | 28.800 bps | ~1996 |
| V.90 | 56.000 bps download / 33.600 bps upload | Assimétrico; downstream via sinal digital na central |
| V.92 | 56.000 bps / 48.000 bps | Upload melhorado |

> [!note] Limite teórico de Shannon
> A capacidade máxima de um canal de voz (~3 kHz, S/N típico) é ~35 kbps. V.90 ultrapassa isso porque a ponta central usa sinalização digital PCM — não é puramente analógico nos dois lados.

### Estabelecimento de Conexão

Discagem por tom (DTMF — *Dual Tone Multi-Frequency*): cada dígito 0–9 + *# é representado pela soma de dois tons de frequências específicas. O modem disca, o modem da central atende, e ocorre um **handshake** (sequência de tons para negociar protocolo, velocidade e compressão).

---

## DSL — Digital Subscriber Line

Aproveita os fios de cobre do par trançado telefônico (POTS — *Plain Old Telephone Service*), mas usa frequências muito além da faixa de voz.

### Por que funciona?

O par trançado suporta frequências de até ~1 MHz (degradando com a distância). A PSTN limitava artificialmente a 3,4 kHz para compatibilidade. DSL usa toda a capacidade do fio.

### ADSL — Asymmetric DSL

Divide a banda em canais de **4 kHz** (DMT — *Discrete Multi-Tone*):

```
0–4 kHz        → POTS (voz telefônica — funciona simultaneamente)
4–25 kHz       → guarda (separação)
25–138 kHz     → upstream (envio): ~26 canais de 4 kHz
138 kHz–1,1 MHz → downstream (recebimento): ~249 canais de 4 kHz
```

Cada canal usa QAM independente — canais com melhor S/N carregam mais bits. Total típico: 8 Mbps down / 1 Mbps up.

**Assimétrico** porque usuários domésticos baixam mais do que enviam (web, streaming). Upstream menor libera mais canais para downstream.

**Splitter**: filtro passivo separa POTS (0–4 kHz) dos dados DSL antes do equipamento doméstico — telefone e Internet usam o mesmo fio ao mesmo tempo.

### Variantes DSL

| Padrão | Down máx | Up máx | Distância máx | Notas |
|---|---|---|---|---|
| **ADSL** | 8 Mbps | 1 Mbps | ~5 km | Padrão residencial |
| **G.lite (ADSL-Lite)** | 1,5 Mbps | 512 kbps | ~5 km | Sem splitter obrigatório; instalação simplificada |
| **ADSL2** | 12 Mbps | 1 Mbps | ~5 km | — |
| **ADSL2+** | 24 Mbps | 3,5 Mbps | ~5 km | Usa até 2,2 MHz |
| **VDSL** | 52 Mbps | 16 Mbps | ~1 km | Muito rápido, distância curta |
| **VDSL2** | 200 Mbps | 100 Mbps | <500 m | Requer fibra até o armário do quarteirão |

> [!note] Distância vs. velocidade
> Quanto mais longe da central/DSLAM, maior a atenuação do sinal → menor velocidade negociada. VDSL é viável apenas em curtas distâncias.

---

## Internet a Cabo (HFC)

Aproveita a infraestrutura de TV a cabo (CATV — *Cable Television*).

### Arquitetura HFC (Hybrid Fiber Coax)

```
[Internet] → [ISP backbone] → [CMTS] → [Fibra óptica] → [Nó de bairro]
                                                                ↓
                                                       [Cabo coaxial]
                                                                ↓
                                               [Divisor (splitter)] → [Residências]
                                                                     → [Residências]
```

- **Fibra óptica**: do ISP ao nó de bairro (centenas de residências)
- **Cabo coaxial**: do nó às residências (última milha) — mesmo cabo da TV
- **CMTS** (*Cable Modem Termination System*): equipamento do ISP que termina as conexões dos modems a cabo

### Canal Compartilhado

O cabo coaxial de um bairro é um **barramento** — todos os assinantes do mesmo nó **compartilham** a largura de banda:
- Downstream: ~600 MHz de largura de banda total, dividida em canais de 6 MHz (EUA) / 8 MHz (Europa)
- Upstream: faixa de 5–42 MHz (menor, porque era de difícil uso no cabo original)

Quando muitos usuários estão ativos simultaneamente, a velocidade efetiva por usuário cai.

### DOCSIS (*Data Over Cable Service Interface Specification*)

Padrão que define como dados trafegam sobre a rede HFC:

| Versão | Down máx | Up máx | Notas |
|---|---|---|---|
| DOCSIS 1.0 | 38 Mbps | 9 Mbps | — |
| DOCSIS 2.0 | 38 Mbps | 27 Mbps | Upstream melhorado |
| DOCSIS 3.0 | 1,2 Gbps | 200 Mbps | Channel bonding (une canais) |
| DOCSIS 3.1 | 10 Gbps | 1 Gbps | OFDM (similar ao ADSL DMT) |

---

## Comparativo

| Tecnologia | Meio | Compartilhado? | Distância | Velocidade típica |
|---|---|---|---|---|
| Dial-up | Par trançado | Não | Ilimitada (PSTN) | 56 kbps |
| ADSL | Par trançado | Não | <5 km | 8–24 Mbps |
| VDSL2 | Par trançado | Não | <500 m | até 200 Mbps |
| Cabo (DOCSIS 3.0) | Coaxial + fibra | **Sim** (por nó) | — | 100–1000 Mbps |
| Fibra FTTH | Fibra óptica | Não | — | 1 Gbps+ |

---

## Ver também

- [[Rede]] — camadas de rede e protocolos TCP/IP
- [[Transmissão de Dados]] — modulação, codificação de linha, simplex/half/full-duplex
- [[Barramento]] — conceito de canal compartilhado (análogo ao cabo coaxial HFC)
- [[Portas de Comunicação]] — interfaces de rede Ethernet; substituiu o dial-up internamente