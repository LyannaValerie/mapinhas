---
title: Monitores de Vídeo
aliases:
  - LCD
  - OLED
  - AMOLED
  - TN
  - VA
  - IPS
  - TFT
  - CCFL
  - QLED
  - nano IPS
  - NanoCell
  - resolução nativa
  - escalonador
  - G-SYNC
  - FreeSync
  - screen tearing
  - input lag
  - tempo de resposta
  - GtG
  - MPRT
  - burn-in
  - DisplayHDR
  - HDR
  - relação de contraste
  - FRC
  - Frame Rate Control
tags:
  - computação/fundamentos
  - computação/vídeo
  - computação/hardware
date: 2026-04-12
source: "Livro Hardware clubedohardware.com.br — Cap. 46"
---

# Monitores de Vídeo

Telas planas: predominantemente **LCD** (cristal líquido) ou **OLED**. Monitores CRT (tubo) descontinuados. Conectados à [[Placa de Vídeo]] via [[Conectores de Vídeo]].

---

## Tecnologia LCD

O painel LCD não emite luz própria — controla a passagem da **luz de fundo** via cristal líquido. Funcionamento TN (referência para as demais tecnologias):

1. Luz de fundo → **polarizador 1** (horizontal) → feixe polarizado horizontalmente
2. Feixe entra no cristal líquido (bastonetes); tensão elétrica torce bastonetes até 90°
3. Torção rotaciona polarização da luz
4. Filtro de cor (R, G ou B) aplica cor ao subpixel
5. **Polarizador 2** (vertical) — filtra: apenas luz torcida (90°) passa; luz não torcida é bloqueada
6. Controle da intensidade: torção intermediária = passagem parcial de luz

### Subpixel
Cada pixel = 3 subpixels (R, G, B) com filtro de cor individual. Cor resultante = mistura das intensidades.

### Tecnologias de Painel LCD

| Característica | TN | VA | IPS |
|---|---|---|---|
| Bastonetes | Paralelos ao substrato | Perpendiculares ao substrato | Paralelos, rotação lateral |
| Tempo de resposta | **Melhor** | Pior | Intermediário |
| Taxa de atualização alta | Tradicional | Possível | Rara |
| Ângulo de visão | **Pior** | Intermediário | **Melhor** |
| Reprodução de cores | **Pior** | Intermediário | **Melhor** |
| Relação de contraste | Padrão | **Melhor** | Padrão |
| Preço | Mais barato | Intermediário | Mais caro |
| Uso ideal | Jogos | Uso geral | Profissionais |

**Nomes alternativos:**
- VA → SVA (Samsung), AMVA (AO/AU Optronics)
- IPS → PLS (Samsung), AHVA (AO/AU Optronics)

> [!note] Profundidade de cores por tecnologia
> - TN: maioria nativa 18 bits (6 bits/cor); 24 bits via FRC
> - VA entrada: 18 bits; intermediário/topo: 24 bits; alguns: 30 bits (raro)
> - IPS entrada: 18 bits; maioria: 24 bits; topo: 30 bits (atenção: alguns 30 bits anunciados são 24 bits + FRC)

### Luz de Fundo

| Tecnologia | Descrição |
|---|---|
| **CCFL** | Lâmpada fluorescente de cátodo frio. Tecnologia antiga. |
| **LED** | Diodos emissores de luz. Menor consumo, tela mais fina. Padrão atual. |
| **QLED** | LED + camada de nanopartículas (pontos quânticos). Amplifica R, G, B → maior fidelidade e gama de cores. Tecnologia de filtro, não é tipo diferente de LED. |
| **nano IPS / NanoCell** | IPS + camada de nanopartículas que absorvem comprimentos de onda excessivos. Gama ~135% sRGB, ~98% DCI-P3. |

> [!warning] "Monitor de LED" ≠ tecnologia nova
> Apenas indica que a luz de fundo usa LEDs. O painel continua sendo LCD.

### Matriz Passiva vs. Ativa

| Tipo | Mecanismo | Resultado |
|---|---|---|
| **Passiva** | Circuito de controle mantém tensão diretamente em cada ponto | Alto tempo de resposta → efeito fantasma. Obsoleta. |
| **Ativa (TFT)** | Cada ponto: transistor TFT (transparente) + capacitor. Capacitor mantém carga entre quadros. | Tempo de resposta baixo. Padrão atual. |

TFT = Thin Film Transistor. Por isso telas LCD ativas também são chamadas **telas TFT**.

---

## Tecnologia OLED

Cada pixel = **3 LEDs orgânicos** (R, G, B). Sem luz de fundo. Sem cristal líquido.

### Vantagens

| Vantagem | Explicação |
|---|---|
| Preto real | Pixels desligados ao exibir preto → zero vazamento de luz |
| Contraste infinito | Brilho do preto = 0 → relação de contraste = ∞ (ver Equação 46.2) |
| Tempo de resposta | LEDs ligam/desligam instantaneamente → <1 ms |
| Ângulo de visão | Superior ao IPS |
| Consumo | Menor, especialmente com conteúdo escuro |
| Espessura | Menor (sem luz de fundo) |

### Desvantagens

| Desvantagem | Detalhe |
|---|---|
| **Preço** | Milhões de LEDs por tela; poucos fabricantes (LG domina mercado de TV) |
| **Degradação** | LEDs perdem brilho com uso; acelerado por brilho alto e exposição UV |
| **Burn-in** | Imagem estática por longos períodos marca a tela permanentemente |

> [!danger] Burn-in em uso como monitor
> Barra de tarefas do Windows ou HUD estático de jogo podem queimar a tela permanentemente. Uso como monitor de computador requer cuidado.

### Variantes OLED

| Variante | Descrição |
|---|---|
| **PMOLED** | Matriz passiva. Limite: ~3 polegadas. Obsoleto (MP3, celulares antigos). |
| **AMOLED** | Matriz ativa. Padrão atual. OLED e AMOLED são sinônimos na prática. |
| **Super AMOLED** | AMOLED Samsung com sensor touch integrado ao painel (não separado). |

---

## Características Técnicas

### Tamanho e Aspecto
- Tamanho: diagonal em polegadas
- Relação de aspecto: ver [[Princípios de Vídeo]]

### Resolução Nativa
LCD e OLED têm **resolução fixa** (pixels físicos imutáveis). Sinal com resolução diferente:
1. **Bordas pretas** — imagem em resolução original centrada
2. **Escalonador** (padrão) — esticar imagem para preencher tela → qualidade reduzida (embaçado)

Sempre configurar placa de vídeo para enviar a **resolução nativa** do monitor.

### Taxa de Atualização e Sincronismo

**Problemas com GPU > Hz do monitor:**
- **Screen tearing** — parte superior e inferior da tela mostram quadros diferentes simultaneamente
- **Input lag** — atraso entre ação do usuário e reflexo na tela

**Vsync:** limita GPU a Hz do monitor → elimina tearing, mas não elimina input lag e pode causar engasgos.

**G-SYNC (NVIDIA) e FreeSync (AMD):** sincronizam taxa de quadros da GPU com a taxa de atualização do monitor dinamicamente. Funcionam como Vsync por hardware. G-SYNC requer **DisplayPort**.

| Tecnologia | Nível | Requisitos |
|---|---|---|
| G-SYNC | Base | Monitor certificado NVIDIA |
| G-SYNC Ultimate | HDR + 1.000 nits + DCI-P3 + Hz máx na resolução máx | Monitor certificado |
| G-SYNC Compatible | FreeSync com suporte G-SYNC | Lista NVIDIA |
| FreeSync | Base | — |
| FreeSync Pro | ≥120 Hz, ≥1080p, LFC | — |
| FreeSync Premium Pro | FreeSync Pro + HDR | — |

LFC = Low Frame rate Compensation: fluidez quando GPU < Hz mínimo do monitor.

### Tempo de Resposta

Tempo para cristal líquido mudar estado (abrir/fechar passagem de luz). Medido em **ms** — menor é melhor. Valor alto → rastro em objetos rápidos.

| Metodologia | O que mede |
|---|---|
| **Ligado-desligado** | 10% (preto) → 90% (branco) → 10% (preto) |
| **GtG (Gray to Gray)** | Transição entre dois tons de cinza; menor deslocamento → valor menor |
| **MPRT** | Tempo de visibilidade do pixel na tela; mínimo = período do quadro (1/Hz) |

> [!warning] GtG não é comparável entre marcas
> Tons de cinza usados não são padronizados. Comparar apenas dentro da mesma marca.

**MPRT mínimo por taxa de atualização:**
- 60 Hz → 16,6 ms
- 144 Hz → 6,9 ms

### Brilho
Medido em **nits** (cd/m²). Para escritório típico: 250–350 cd/m². Ambientes externos / luz solar direta: valor muito maior necessário.

### Relação de Contraste

$$\text{Relação de contraste} = \frac{\text{Brilho do branco mais brilhante}}{\text{Brilho do preto mais escuro}}$$

Maior = melhor. LCD: preto nunca 100% bloqueado → contraste limitado. OLED: pixel desligado = 0 → contraste infinito.

| Tipo | Explicação |
|---|---|
| **Estática / nativa** | Característica física do painel. Valores reais: abaixo de 3000:1. |
| **Dinâmica (DC)** | Mede com brilho reduzido durante cenas escuras. Não padronizado; valores fantasiosos. |

> [!warning] Contraste dinâmico
> Fabricantes anunciam majoritariamente DC. Números acima de ~3000:1 são irrelevantes na prática.

**DisplayHDR True Black:** brilho do preto ≤ 0,0005 cd/m² → alta relação de contraste estática garantida.

### Profundidade de Cores e FRC
Ver [[Princípios de Vídeo]]. Painel pode ser nativo em 18 bits e aceitar entrada de 24 bits usando **FRC** (Frame Rate Control — interpolação de quadros para simular cores ausentes).

### HDR (High Dynamic Range)
Imagens com mais detalhe em sombras e luzes, maior quantidade de cores e luminosidade.

**Certificação DisplayHDR (VESA):**

| Certificação | Brilho de pico | Profundidade nativa |
|---|---|---|
| DisplayHDR 400 | 400 cd/m² | 8 bits (24 bits totais) |
| DisplayHDR 500+ | 500+ cd/m² | 10 bits (30 bits totais) |
| DisplayHDR True Black | variável + preto ≤ 0,0005 cd/m² | 10 bits (30 bits totais) |

### Ângulo de Visão
Ângulo máximo em relação à tela para ainda visualizar o conteúdo. Dois valores: horizontal e vertical. Valores divulgados indicam apenas "consegue ver algo" — não garantem qualidade da imagem.

### Tela Curva
Curvatura medida em raio (mm) + "R". Menor raio = maior curvatura. O raio também indica distância recomendada de uso em mm.

Exemplo: **2300R** → mais curvo que **3000R**; distância ideal ≤ 2,3 m.

### Conexões
Ver [[Conectores de Vídeo]] (VGA, DVI, HDMI, DisplayPort).

---

## Relações (SPO)

- LCD → controla → passagem de luz de fundo via cristal líquido (bastonetes)
- TN → tem → menor tempo de resposta; pior ângulo de visão e cores
- VA → tem → melhor contraste; maior tempo de resposta
- IPS → tem → melhor ângulo de visão e cores; pior contraste que VA
- TFT → é → transistor de filme fino; ativa matriz ativa LCD
- OLED → usa → LEDs por pixel; sem luz de fundo; preto real
- OLED → sofre → burn-in com imagens estáticas
- QLED → é → LCD com filtro de nanopartículas; não é tipo de LED diferente
- nano IPS → é → IPS com nanopartículas absorvedoras de comprimento de onda excessivo
- FRC → simula → cores não nativas do painel via interpolação de quadros
- G-SYNC / FreeSync → sincronizam → taxa de quadros GPU com Hz do monitor
- G-SYNC → exige → conexão DisplayPort
- DisplayHDR → certifica → luminância de pico e profundidade de cores nativa

---

## Ver também

- [[Princípios de Vídeo]] — pixel, resolução, taxa de atualização, profundidade de cores, espaço de cores, gama de cores, relação de aspecto
- [[Conectores de Vídeo]] — VGA, DVI, HDMI, DisplayPort (saída da placa → entrada do monitor)
- [[Placa de Vídeo]] — RAMDAC, GPU, geração do sinal de vídeo
- [[Aceleração 3D]] — Vsync por software; G-SYNC/FreeSync complementam
