---
title: Fonte de Alimentação
aliases:
  - PSU
  - Power Supply
  - SMPS
  - Fonte Chaveada
  - ATX12V
tags:
  - hardware
  - alimentação
  - eletrônica
date: 2026-04-12
source: "Clube do Hardware - Cap. 57"
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Hardware]]

# Fonte de Alimentação

Converte tensão alternada da rede elétrica nas tensões contínuas necessárias para o computador. Tipo: **fonte chaveada** (SMPS — Switch-Mode Power Supply), também chamada conversor CC-CC ou DC-DC. Não confundir com fonte linear (princípio completamente diferente).

> [!warning] Componente mais negligenciado
> Fonte de baixo custo causa: maior consumo elétrico, comportamento errático (travamentos, BSOD, reinicializações), throttling de GPU, e pode danificar outros componentes.

---

## Padrões

Todos os padrões foram criados ou coordenados pela Intel. São acumulativos (ATX12V complementa ATX; EPS12V complementa ATX12V, etc.).

| Padrão        | Ano  | Escopo               | Principal adição                                      |
| ------------- | ---- | -------------------- | ----------------------------------------------------- |
| ATX           | 1995 | Fonte + gabinete + mobo | Conector 20 pinos, +5VSB (desligamento por software) |
| ATX12V 1.0    | 2000 | Fonte desktop        | Conector ATX12V 4-pin (CPU via +12V)                  |
| ATX12V 1.2    | 2002 | –                    | Saída -5V opcional                                    |
| ATX12V 1.3    | 2003 | –                    | Saída -5V removida; conector SATA opcional            |
| EPS12V        | 2000 | Fonte servidor       | Conector principal 24 pinos; conector CPU 8 pinos     |
| ATX12V 2.0    | 2003 | Fonte desktop        | Conector principal 24 pinos; SATA obrigatório         |
| ATX12V 2.51   | 2017 | –                    | Conector EPS12V e conectores GPU oficializados; OTP obrigatório |
| ATX12V 2.52   | 2018 | –                    | Remove +3,3V do conector SATA                         |
| ATX12VO       | 2020 | Fonte desktop        | Apenas +12V; retira +5V/+3,3V/-12V; +12VSB em vez de +5VSB |
| ATX 3.0       | 2022 | Unificado            | Conector 12VHPWR (12+4 pinos) para GPU; unifica ATX + ATX12V 2.53 + EPS12V 2.92 |

> [!note] ATX12VO
> Tensões +5V e +3,3V migram para conversores DC-DC na placa-mãe. Conectores SATA e periféricos saem da mobo, não da PSU. Pouca adoção no mercado.

---

## Potência

Classificação em watts (W). Atenção:

| Armadilha de mercado           | Detalhe                                                     |
| ------------------------------ | ----------------------------------------------------------- |
| Potência de pico               | Como "PMPO" de caixas de som — sustentável por frações de segundo |
| Potência a 25 °C               | Capacidade cai com temperatura; dentro do PC está bem acima de 25 °C |
| Etiqueta fantasiosa            | Comum no Brasil; crime: fraude (CP art. 171) + propaganda enganosa (CDC art. 66) |
| Inmetro                        | Não homologa fontes — só o cabo de força. Selos de Inmetro em fontes são falsos |

**Recomendação:** comprar fonte com **o dobro da potência** necessária.
- Maior eficiência operando em 40–60% da carga nominal
- Margem para upgrades futuros
- Componentes mais frios = maior vida útil

Ferramenta de cálculo: `outervision.com/power-supply-calculator`

---

## Eficiência

$$\eta = \frac{P_o}{P_i}$$

$P_o$ = potência entregue ao computador; $P_i$ = potência consumida da rede.

Maior eficiência em 40–60% de carga. Eficiência maior em 220 V do que 127 V.

### Certificação 80 Plus

Testes a 23 °C (crítica: temperatura baixa demais).

| Nível     | 20%  | 50%  | 100% |
| --------- | ---- | ---- | ---- |
| Padrão    | 80%  | 80%  | 80%  |
| Bronze    | 82%  | 85%  | 82%  |
| Silver    | 85%  | 88%  | 85%  |
| Gold      | 87%  | 90%  | 87%  |
| Platinum  | 90%  | 92%  | 89%  |
| Titanium  | 92%  | 94%  | 90%  |

Valores para 115 V. UE (230 V) tem requisitos ligeiramente maiores. Titanium adiciona nível de 10% (90%).

> [!warning] Selos falsos
> Verificar em `80plus.org` se a fonte foi realmente certificada.

### Certificação Cybenetics ETA

Testes a 30–34 °C; mede +5VSB e consumo vampiro; 1.450+ combinações de carga; 25.000 leituras.

| Nível    | Eficiência 115V | +5VSB   | Vampiro    |
| -------- | --------------- | ------- | ---------- |
| Bronze   | 82%             | 71%     | ≤ 0,25 W   |
| Silver   | 85%             | 73%     | ≤ 0,22 W   |
| Gold     | 87%             | 75%     | ≤ 0,19 W   |
| Platinum | 89%             | 76%     | ≤ 0,16 W   |
| Titanium | 91%             | 77%     | ≤ 0,13 W   |
| Diamond  | 93%             | 79%     | ≤ 0,10 W   |

Cybenetics também certifica ruído: **LAMBDA**.

---

## Fator de Potência (PFC)

$$FP = \frac{\text{Energia ativa}}{\text{Energia aparente}}$$

- Energia ativa: produz trabalho (kWh)
- Energia reativa: necessária a motores/transformadores, mas não produz trabalho (kVArh)
- Energia aparente: composição das duas (kVAh)

> [!note] PFC não reduz conta de luz doméstica
> Concessionárias não cobram energia reativa de residências/comércio. PFC é obrigatório apenas em contextos industriais onde há medição de reativa.

| Tipo PFC  | FP típico   | Característica                                          |
| --------- | ----------- | ------------------------------------------------------- |
| Sem / Passivo | 0,6–0,7 | Bobina grande e pesada; projeto mais antigo             |
| Ativo     | 0,90–0,99   | Bobina menor + transistores; projeto moderno; full range |

Fontes com PFC ativo são, na prática, **universais (full range)**: operam de 100 V a 240 V sem chave seletora. Não precisam de estabilizador de tensão.

> [!warning] Bivolt ≠ Full range
> "Bivolt" = opera em DUAS tensões (115V ou 230V) via chave. "Full range" = opera em QUALQUER tensão de 100V–240V. Chamar full range de bivolt é erro grosseiro.

---

## Saídas

### ATX, ATX12V, EPS12V

| Saída    | Cor do fio | Função                                                      |
| -------- | ---------- | ----------------------------------------------------------- |
| +12 V    | Amarelo    | CPU, GPU — principal saída                                   |
| +5 V     | Vermelho   | Periféricos, lógica                                         |
| +3,3 V   | Laranja    | Memória, lógica                                             |
| -12 V    | Azul       | Usos legacy                                                 |
| +5VSB    | Roxo       | Stand-by: alimenta mesmo desligado (USB carregamento, etc.) |
| PWR_OK   | Cinza      | Sinaliza à mobo que tensões estão estáveis (power good)     |
| PS_ON#   | Verde      | Liga a fonte quando posto em "0"; curtar verde com preto = ligar fora do PC |
| Terra    | Preto      | —                                                           |

### ATX12VO

| Saída    | Cor  | Observação                         |
| -------- | ---- | ---------------------------------- |
| +12 V    | Amarelo | Única tensão de potência         |
| +12VSB   | Roxo | Substitui o +5VSB                  |
| PWR_OK   | Cinza | —                                 |
| PS_ON#   | Verde | —                                 |

### Configuração do Barramento +12 V

Norma UL-60950-1: circuitos > 240 VA (= 240 W em CC) necessitam proteção OCP → limita saída +12V a 240 W / 20 A por barramento.

| Configuração          | Proteção OCP              | Risco                                    |
| --------------------- | ------------------------- | ---------------------------------------- |
| Barramento único      | Um único OCP; limite alto | Sem restrição por dispositivo            |
| Múltiplos barramentos | OCP por barramento (≤20A) | Pode desligar em uso normal com muitas GPUs |

---

## Oscilação e Ruído (Ripple & Noise)

| Saída  | Limite máximo (Vpp) |
| ------ | ------------------- |
| +12 V  | 120 mV              |
| +5 V   | 50 mV               |
| +3,3 V | 50 mV               |
| +5VSB  | 50 mV               |
| -12 V  | 120 mV              |

Fontes acima desses limites causam problemas aleatórios. Testar com carga real (jogo pesado), não em vazio.

---

## Regulação de Tensão

| Saída  | Tolerância ATX12V | Mín.      | Máx.      |
| ------ | ----------------- | --------- | --------- |
| +12 V  | ±5%               | +11,40 V  | +12,60 V  |
| +5 V   | ±5%               | +4,75 V   | +5,25 V   |
| +3,3 V | ±5%               | +3,13 V   | +3,46 V   |
| +5VSB  | ±5%               | +4,75 V   | +5,25 V   |
| -12 V  | ±10%              | -10,80 V  | -13,20 V  |

ATX 3.0 amplia tolerância do +12V para -7% a +5% (−8% nos conectores GPU para suportar picos de consumo).

---

## Ventilação

| Posição da ventoinha       | Tamanho | Observação                              |
| -------------------------- | ------- | --------------------------------------- |
| Traseira                   | 80 mm   | Configuração clássica                   |
| Inferior                   | 120/135/140 mm | Preferível: maior fluxo, menos ruído |
| Passiva (sem ventoinha)    | —       | Mais cara; zero ruído; uso em PCs silenciosos |

Controle térmico: velocidade via sensor térmico no dissipador do secundário. Algumas fontes desligam ventoinha com baixa carga.

---

## Dimensões Físicas

| Padrão          | L (mm)    | A (mm) | P (mm)       |
| --------------- | --------- | ------ | ------------ |
| ATX / ATX12V / EPS12V | 150  | 86     | 140 (EPS até 230) |
| SFX12V          | 100       | 63,5   | 125          |
| SFX12V Perfil Baixo | 100  | 50     | 125          |
| TFX12V          | 85        | 65     | 175          |
| Flex ATX        | 81,5      | 40,5   | 150          |
| LFX12V          | 72        | 62     | Até 210      |
| CFX12V          | 150/101,6 | 86     | 95           |

---

## Cabos e Conectores

### Bitola dos Fios

| Bitola AWG | Uso                              | Qualidade   |
| ---------- | -------------------------------- | ----------- |
| 22/20 AWG  | Fontes ruins (fio fino)          | Ruim        |
| 18 AWG     | Mínimo obrigatório               | Padrão      |
| 16 AWG     | Fontes ≥ 750W de qualidade       | Bom         |

AWG: valores maiores = fio mais fino (inverso ao mm²).

### Cabeamento Modular

- **Parcial**: cabos principais fixos, demais removíveis
- **Completo**: todos removíveis
- Benefício: reduz cabos internos → melhora ventilação

### Conector Principal da Placa-Mãe

| Pinos | Padrão                            |
| ----- | --------------------------------- |
| 20    | ATX, ATX12V 1.x (em desuso)       |
| 24    | ATX12V 2.x, EPS12V, ATX 3.0      |
| 10    | ATX12VO (+ auxiliar 6-pin se necessário) |

### Conector CPU (Processador)

| Nome informal | Pinos | Padrão origem |
| ------------- | ----- | ------------- |
| ATX12V        | 4     | ATX12V 1.0    |
| EPS12V        | 8     | EPS12V        |

Alguns conectores EPS12V partem ao meio → viram ATX12V.

### Conectores GPU (Placas de Vídeo)

| Conector         | Pinos  | Lançamento | Potência máxima                     |
| ---------------- | ------ | ---------- | ----------------------------------- |
| 6 pinos (2x3)    | 6      | 2004       | 75 W                                |
| 8 pinos (2x4)    | 8      | 2008       | 150 W                               |
| 12 pinos NVIDIA  | 12     | 2020       | 500 W (18 AWG) / 600 W (16 AWG)     |
| 12VHPWR (12+4)   | 16     | 2022 (ATX 3.0) | 150 / 300 / 450 / 600 W (configurável pelos 4 pinos de sinal) |

12VHPWR obrigatório em fontes ATX 3.0 acima de 450 W.

### Conector SATA

Para HDs, SSDs 2,5", ópticas. A partir de SATA 3.2 (2013): sem pino laranja (+3,3V).

> [!warning] Adaptadores periférico → SATA
> Mercado inundado de adaptadores ruins que derretem. Evitar.

### Conector Periférico ("Molex")

Usado em HDs/ópticas PATA antigos, ventoinhas, iluminação, refrigeração líquida. "Molex" é nome de fabricante de conectores (uso incorreto como nome genérico).

---

## Arquitetura (SMPS)

### Topologias

| Topologia  | Bobina                  | Uso                                    |
| ---------- | ----------------------- | -------------------------------------- |
| Buck       | Em série após diodo     | Abaixadora — secundário de PSUs de PC  |
| Boost      | Em série antes do diodo | Elevadora — PFC ativo; inversor CCFL   |
| Buck-Boost / Flyback | Em paralelo  | Elevadora — monitores CRT              |

### Fluxo de Sinal (configuração meia-ponte)

```
CA entrada
  → Filtragem de transientes (EMI)
  → Retificação (ponte retificadora)
  → [PFC ativo → ~400V CC] ou [Dobrador de tensão]
  → Chaveamento (transistores MOSFET/bipolar, 20 kHz+)
  → Transformador (redução de tensão)
  → Retificação secundário (diodos Schottky + bobina)
  → Filtragem secundário (bobina + capacitor)
  → Saídas CC
  → Realimentação → Controlador PWM
```

### Chaveamento: Configurações

| Configuração            | Transistores | Característica                              |
| ----------------------- | ------------ | ------------------------------------------- |
| Meia-ponte              | 2 bipolares  | Projeto antigo; menos eficiente             |
| Chaveamento direto (1T) | 1 MOSFET     | Enrolamento de reset necessário             |
| Chaveamento direto (2T) | 2 MOSFET     | Sem enrolamento de reset; diodos de desmagnetização |
| Ressonante série (LLC)  | 2 MOSFET     | Alta eficiência                             |
| Ressonante paralelo (LLC) | 2 MOSFET  | Alta eficiência                             |
| Ressonante série-paralelo (LCC) | 2 MOSFET | Alta eficiência                       |
| Ponte completa          | 4 MOSFET     | Mais caro                                   |
| Push-pull               | 2 MOSFET     | Transistores alternados                     |
| Active clamp            | 2 MOSFET     | Ciclo de carga > 50%; alta eficiência       |

MOSFET: maior eficiência que bipolar; parâmetro chave = **RDS(on)** (resistência dreno-source em condução — menor = melhor).

### Projeto DC-DC / CC-CC

Fontes de alta eficiência geram +5V e +3,3V via conversores chaveados alimentados pela saída +12V (em vez de enrolamentos separados no transformador).

### Retificação Síncrona

Diodos Schottky substituídos por transistores MOSFET no secundário → maior eficiência.

---

## Proteções

| Proteção | Nome EN         | Obrigatória?          | Monitora                              |
| -------- | --------------- | --------------------- | ------------------------------------- |
| OPP/OLP  | Over Power/Load | Opcional              | Potência total consumida da rede      |
| OCP      | Over Current    | Obrigatória (entrada via fusível; saída via circuito) | Corrente por barramento |
| UVP      | Under Voltage   | Entrada: obrigatória; Saída: opcional | Tensão abaixo do mínimo |
| Brown out | —              | Obrigatória           | Queda de tensão da rede               |
| OVP      | Over Voltage    | Entrada: opcional; Saída: obrigatória | Tensão acima do máximo |
| SCP      | Short-Circuit   | Obrigatória           | Curto em qualquer saída               |
| OTP      | Over Temperature | Obrigatória (ATX12V 2.51+) | Temperatura interna              |
| NLO      | No-Load Operation | Requerimento (não proteção) | Liga sem carga — óbvio       |

---

## Relações

| Sujeito            | Predicado        | Objeto                       |
| ------------------ | ---------------- | ---------------------------- |
| PSU                | alimenta         | [[Placa-mãe]]                |
| PSU                | alimenta         | [[Processador]] (via ATX12V/EPS12V) |
| PSU                | alimenta         | [[Placa de Vídeo]] (via PCI-E 6/8/12+4) |
| PSU                | alimenta         | [[Disco Rígido]] (via SATA/periférico) |
| PSU                | alimenta         | [[SSD]] (via SATA)           |
| PFC ativo          | torna PSU        | full range (universal)       |
| ATX12VO            | transfere conversão DC-DC para | [[Placa-mãe]]   |
| Eficiência 80 Plus | certifica        | η mínima por nível           |
| PSU chaveada (SMPS) | diferente de    | fonte linear                 |
| Conector 12VHPWR   | introduzido em   | ATX 3.0                      |
| PSU                | parte do sistema de exaustão de | [[Gabinete]]  |
| Ventoinha PSU      | funciona como    | [[Ventoinha]] de exaustão    |

---

## Ver também

- [[Placa-mãe]] — conectores de alimentação; formatos ATX
- [[Processador]] — TDP; conector CPU 4/8 pinos
- [[Placa de Vídeo]] — conectores auxiliares 6/8/12+4 pinos
- [[Disco Rígido]] — conector SATA power
- [[SSD]] — conector SATA power
- [[Gabinete]] — posicionamento PSU; fan-down vs fan-up
- [[Ventoinha]] — ventoinhas da PSU; impacto na exaustão do case
- [[Cooler]] — TDP; consumo do sistema
- [[Equipamentos de Proteção]] — nobreak; filtro de linha; estabilizador (não usar)
- [[Bateria]] — tecnologias; VRLA em nobreaks
