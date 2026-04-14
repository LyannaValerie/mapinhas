---
title: Mouse
aliases:
  - Mouse Óptico
  - Mouse Laser
  - Mouse Mecânico
tags:
  - hardware
  - entrada
  - periférico
date: 2026-04-12
source: "Clube do Hardware - Cap. 56"
---

# Mouse

Dispositivo apontador. Move cursor na tela. Classificado pelo tipo de sensor.

## Tipos de Sensor

| Tipo      | Mecanismo                               | Superfície        | Luz visível |
| --------- | --------------------------------------- | ----------------- | ----------- |
| Mecânico  | Esfera rolante → encoders internos      | Qualquer (suja)   | —           |
| Óptico    | LED (normalmente vermelho) + sensor CCD | Maioria; não vidro | Vermelho   |
| Laser     | Laser infravermelho + sensor            | Qualquer          | Nenhuma     |

> [!note] Identificação rápida
> Luz vermelha visível = óptico. Sem luz visível (não mecânico) = laser.

**Trackball**: esfera movida pela mão do usuário, lida como mouse mecânico invertido.

---

## Especificações Técnicas

### Resolução — dpi (dots per inch)

Pixels que o cursor percorre por polegada de movimento físico.

**Exemplo:**
| dpi  | Pixels/polegada de movimento |
| ---- | ---------------------------- |
| 400  | 400 px                       |
| 800  | 800 px                       |
| 1200 | 1200 px                      |

> Maior dpi = cursor mais sensível ao movimento. Modelos avançados têm seletor de dpi ajustável.

> [!warning] Interpolação
> Resolução nominal alta pode ser obtida por interpolação, não por hardware real. Verificar modelo do sensor — mais importante que o número de dpi.

### Velocidade de Rastreamento — ips (inches per second)

Velocidade máxima de movimento que o sensor consegue capturar sem pular informações.

- < 60 ips: uso de escritório
- ≥ 60 ips: uso para jogadores

### Aceleração — G

Variação de velocidade máxima suportada (1 G ≈ 9,8 m/s²).

- Ex: 30 G ≈ 294 m/s²
- Maior aceleração = mouse tolerante a movimentos bruscos

### Taxa de Varredura — Hz

Quantas vezes por segundo o SO lê o mouse.

| Taxa     | Intervalo de leitura | Uso          |
| -------- | -------------------- | ------------ |
| 125 Hz   | 8 ms                 | Padrão       |
| 1000 Hz  | 1 ms                 | Gaming       |

Maior taxa = maior precisão em jogos, maior custo.

---

## Botões

- Padrão: 2 botões + scroll (clicável = 3º botão)
- Modelos avançados: botões extras configuráveis (funções, macros, autorrepetição)

### Tipo de Botão

| Tipo      | Mecanismo                        | Atraso  | Uso             |
| --------- | -------------------------------- | ------- | --------------- |
| Mecânico  | Interruptor físico + debounce    | ~ms     | Maioria         |
| Óptico    | Sensor óptico, sem contato físico | ~zero  | Gaming competitivo |

**Bouncing (mecânico)**: ruído elétrico no momento do acionamento exige tempo de debounce antes de enviar sinal ao computador. Botão óptico elimina esse atraso.

---

## Outras Características

### Memória Interna

Armazena configurações (dpi, cores RGB, macros, funções de botões) — permite migrar entre computadores sem reconfigurar.

### Ajustes Físicos

Alguns modelos permitem ajuste de peso e/ou altura.

### Iluminação

LED cor única ou RGB configurável. Puramente estético — não relacionado ao sensor.

### Ergonomia / Destro × Canhoto

Muitos modelos são assimétricos para mão direita. Verificar suporte ambidestro se necessário.

---

## Mouse Gamer (Gaming Mouse)

Para ser genuinamente voltado a jogadores, deve apresentar:
- Sensor de qualidade (verificar modelo, não só dpi)
- Alta resolução real (sem interpolação)
- Velocidade de rastreamento ≥ 60 ips
- Alta aceleração
- Alta taxa de varredura (≥ 1000 Hz)
- Botões com baixo debounce (ou ópticos)

> [!note] Nomenclatura
> "Mouse gamer" = "mouse jogador" — construção incorreta em português. Correto: "mouse para jogadores" / em inglês: "gaming mouse".

---

## Conexão

| Tipo     | Alimentação    | Observação                                          |
| -------- | -------------- | --------------------------------------------------- |
| PS/2     | Pelo PC        | Raro em placas recentes                             |
| USB      | Pelo PC        | Padrão atual                                        |
| Sem fio  | Pilhas AA/AAA ou bateria recarregável | Receptor USB; alguns unificam receptor com teclado |
| Bluetooth | Pilhas/recarregável | Requer receptor BT no computador              |

---

## Relações

| Sujeito           | Predicado       | Objeto                    |
| ----------------- | --------------- | ------------------------- |
| Mouse laser       | supera em precisão | Mouse óptico           |
| dpi               | mede            | resolução do sensor       |
| Taxa de varredura | afeta           | latência de leitura       |
| Botão óptico      | elimina         | atraso de debounce        |
| Mouse             | conecta via     | [[Portas de Comunicação]] |
| Mouse sem fio     | pode compartilhar receptor com | [[Teclado]]  |

---

## Ver também

- [[Teclado]] — dispositivo de entrada complementar; conexões PS/2/USB/sem fio
- [[Portas de Comunicação]] — PS/2, USB, Bluetooth
- [[Monitores de Vídeo]] — resolução da tela afeta sensação de dpi do mouse
