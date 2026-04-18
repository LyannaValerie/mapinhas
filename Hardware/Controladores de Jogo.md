---
title: Controladores de Jogo
aliases:
  - Wiimote
  - Kinect
  - controle de videogame
  - game controller
  - sensor de movimento
  - acelerômetro
  - giroscópio
  - rastreamento esqueletal
tags:
  - computação/hardware
date: 2026-04-17
source: "Organização Estruturada de Computadores — Tanenbaum, Cap. 2"
---

# Controladores de Jogo

Dispositivos de entrada especializados para videogames. Evoluíram de simples botões para sensores de movimento e visão computacional.

## Relações (SPO)

- Wiimote → detecta → movimento via acelerômetro MEMS + sensor IR
- Kinect → combina → câmera RGB + câmera de profundidade + array de microfones
- Sensor de profundidade → usa → luz estruturada (projetor IR + câmera IR)
- Rastreamento esqueletal → extrai → 20 articulações de 2 pessoas simultâneas

---

## Wiimote (Nintendo Wii, 2006)

Controle sem fio com detecção de movimento, revolucionou a interação em videogames.

### Componentes

| Componente | Função |
|---|---|
| **Acelerômetro MEMS (3 eixos)** | Mede aceleração linear em x, y, z — detecta inclinação e movimentos bruscos |
| **Sensor IR (câmera infravermelha)** | Enxerga a Sensor Bar (2 LEDs IR na TV) → calcula posição apontada na tela |
| **Botões** | A, B, D-pad, 1, 2, Home, +, − |
| **Rumble (vibração)** | Motor com peso excêntrico |
| **Speaker interno** | Pequeno alto-falante para efeitos sonoros |
| **Bluetooth** | Comunicação com o console |
| **Expansão** | Porta para acessórios (Nunchuck, MotionPlus) |

### Acessórios

**Nunchuck:** conecta na porta de expansão. Adiciona joystick analógico, botões C e Z, e segundo acelerômetro — para movimentos com a mão não dominante.

**Wii MotionPlus:** giroscópio de 3 eixos complementa o acelerômetro. Diferença:
- Acelerômetro → força/aceleração (não detecta rotação pura)
- Giroscópio → velocidade angular → detecta torção e rotação

Combinados, formam uma unidade de medição inercial (IMU) completa — base de controles modernos e VR.

### Limitações do Acelerômetro Isolado
Um acelerômetro puro não distingue gravidade de aceleração de movimento. Sem giroscópio, pequenas rotações passam despercebidas. Por isso o MotionPlus foi necessário para jogos de precisão.

---

## Kinect (Microsoft Xbox 360/One, 2010/2013)

Câmera de jogo sem controle físico — o corpo do usuário é o controle.

### Sensores

| Sensor | Especificação | Função |
|---|---|---|
| **Câmera RGB** | 640×480, 30 fps | Imagem colorida do ambiente |
| **Câmera IR** | 320×240 | Captura padrão IR refletido |
| **Projetor IR** | Grade de pontos IR | Projeta padrão conhecido no ambiente |
| **Array de microfones** | 4 microfones lineares | Localização 3D de som (beamforming) |

### Câmera de Profundidade — Luz Estruturada

```
[Projetor IR] → projeta grade de pontos no ambiente
[Câmera IR]   → captura grade deformada pela geometria da cena
[Processador] → compara padrão projetado vs. capturado → calcula profundidade por triangulação
```

Resultado: **mapa de profundidade** — cada pixel tem distância em mm além de cor.

### Rastreamento Esqueletal

O processador do Kinect (ARM + GPU) extrai em tempo real:
- **2 pessoas simultâneas**
- **20 articulações** por pessoa (cabeça, pescoço, ombros, cotovelos, pulsos, mãos, quadril, joelhos, tornozelos, pés)
- Pose completa do corpo → avatar 3D controlado por movimento real

Algoritmo usa floresta de decisão aleatória (random decision forest) treinado com milhões de imagens de profundidade sintéticas — abordagem de aprendizado de máquina pioneira para hardware de consumo.

### Array de Microfones

4 microfones espaçados fisicamente + processamento de sinal:
- **Beamforming**: combina os 4 sinais para "apontar" o microfone virtualmente para a fonte de som
- **Cancelamento de eco**: remove o som dos alto-falantes da TV da captura
- **Reconhecimento de voz**: "Xbox, pause!"

---

## Evolução Histórica

| Geração | Tecnologia | Exemplo |
|---|---|---|
| 1ª | Botões digitais | D-pad, A/B |
| 2ª | Analógico + vibração | DualShock (PS1, 1997) |
| 3ª | Sensor de movimento | Wiimote (2006) |
| 4ª | Visão computacional | Kinect (2010) |
| Atual | IMU + tracking 6DOF | Controles VR (Quest, Index) |

---

## Ver também

- [[Mouse]] — outro dispositivo apontador; tracking óptico
- [[Monitores de Vídeo]] — saída visual dos jogos
- [[Instruções SIMD]] — processamento vetorial para física e gráficos de jogo
- [[Portas de Comunicação]] — USB e Bluetooth como interfaces de controles modernos