---
title: Princípios de Vídeo
aliases:
  - pixel
  - resolução de vídeo
  - taxa de atualização
  - varredura progressiva
  - varredura entrelaçada
  - profundidade de cores
  - subamostragem de cores
  - espaço de cores
  - gama de cores
  - RGB
  - RGBA
  - chroma subsampling
  - luma
  - croma
tags:
  - computação/fundamentos
  - computação/vídeo
date: 2026-04-12
source: "Livro Hardware clubedohardware.com.br — Cap. 41"
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Hardware]]

# Princípios de Vídeo

Conceitos básicos comuns a placas de vídeo, monitores e conexões de vídeo.

## Relações (SPO)

- Pixel → é → menor ponto desenhável na tela (3 subpixels: R, G, B)
- Varredura → percorre → tela da esquerda para direita, de cima para baixo
- Resolução → é → número de pixels em colunas × linhas
- Profundidade de cor → determina → número de cores possíveis por pixel (2ⁿ)
- Taxa de atualização → mede → quadros por segundo enviados ao monitor (Hz)
- Subamostragem de cores → reduz → largura de banda sacrificando informação de croma
- Espaço de cores → define → subconjunto das cores perceptíveis pelo olho humano
- Gama de cores → indica → percentual do espaço de cores que o dispositivo reproduz

---

## Pixel

Menor ponto desenhável na tela. Em monitores coloridos: formado por 3 subpixels nas cores primárias de vídeo **RGB** (Red, Green, Blue).  
Cor percebida = mistura das três cores primárias em proporções variáveis.

---

## Varredura

Processo de acender pixels da esquerda para direita e de cima para baixo.

### Progressiva (não entrelaçada)

Varre todas as linhas em sequência: linha 1, 2, 3 … N.  
**1 quadro = N linhas completas.**  
Padrão atual em computadores e monitores modernos.

### Entrelaçada

Cada quadro original dividido em 2 campos:
- Campo ímpar: linhas 1, 3, 5 …
- Campo par: linhas 2, 4, 6 …

**1 quadro = metade das linhas por campo.** Criada para TV: captura a 30 fps, exibe a 60 campos/s (ilusão de 60 fps com metade da largura de banda).

| Característica | Progressiva | Entrelaçada |
|---|---|---|
| Linhas por quadro | Todas | Metade |
| Qualidade de imagem | Melhor | Pior |
| Largura de banda | Maior | Menor |
| Uso atual | Monitores/PCs | Conteúdo legado de TV |

> Formato de resolução: `xxxp` = progressiva, `xxxi` = entrelaçada. Ex: `1080p`, `1080i`.  
> Tempo entre fim de um quadro e início do próximo = **tempo de retraço**.

---

## Resolução

Número de pixels: colunas × linhas.

### Resoluções mais populares

| Nome | Largura | Altura |
|---|---|---|
| VGA | 640 | 480 |
| HD | 1280 | 720 |
| Full HD | 1920 | 1080 |
| QHD | 2560 | 1440 |
| 4K UHD | 3840 | 2160 |
| DCI 4K | 4096 | 2160 |
| 5K | 5120 | 2880 |
| 8K UHD | 7680 | 4320 |

> Resolução nativa = resolução máxima do monitor. Usar a resolução nativa = melhor qualidade. Placas de vídeo não podem exibir resolução maior que o monitor suporta.

---

## Relação de Aspecto

Largura ÷ Altura. Exemplos:

| Padrão | Relação | Valor |
|---|---|---|
| 4:3 | 1,33 | Monitores antigos |
| 5:4 | 1,25 | — |
| 16:9 | 1,77 | Padrão atual (widescreen) |
| 16:10 | 1,6 | Widescreen profissional |

Full HD 1920×1080 → 1920÷1080 = 1,77 = **16:9**.

---

## Profundidade de Cores

Número de bits por pixel → determina quantidade de cores: 2ⁿ.

| Bits totais | Bits por canal RGB | Cores |
|---|---|---|
| 8 | — | 256 |
| 16 | — | 65.536 |
| 24 | 8 | 16.777.216 |
| 30 | 10 | 1.073.741.824 |
| 32 (RGBA) | 8 + 8 alfa | 16.777.216 (alfa = transparência, não enviado ao monitor) |
| 32 (CMYK) | 8 × 4 | 4.294.967.296 (impressão) |
| 36 | 12 | 68.719.476.736 |
| 48 | 16 | 281.474.976.710.656 |

> "8 bits" e "24 bits" significam o mesmo (8 = por canal, 24 = total RGB).  
> "10 bits" = "30 bits". Uso profissional (edição de vídeo/imagem).

### Canal Alfa (RGBA)

32 bits RGBA = 24 bits cor + 8 bits transparência.  
Transparência usada internamente pela placa de vídeo; **não enviada ao monitor**.  
RGBA 32 bits apresenta o mesmo número de cores que RGB 24 bits.

### CMYK vs. RGB

- **RGB**: cores primárias de vídeo (monitor)
- **CMYK** (Ciano, Magenta, Amarelo, Preto): cores primárias de impressão
- Conversão RGB→CMYK pode distorcer cores; profissionais gráficos trabalham diretamente em CMYK

---

## Espaço de Cores e Gama de Cores

**Espaço de cores**: padrão que define subconjunto das cores perceptíveis pelo olho humano.  
**Gama de cores**: percentual do espaço de cores que o dispositivo reproduz de fato.

| Espaço de cores | Cobertura relativa |
|---|---|
| sRGB | Padrão web/consumo |
| NTSC 1953 | TV analógica |
| DCI-P3 | Cinema digital |
| Adobe RGB | Fotografia/impressão profissional |
| REC.2020 | HDR / 4K / futuro |

> REC.2020 > Adobe RGB > DCI-P3 > sRGB (em cobertura do espectro visível).  
> Monitor reproduzindo 80% sRGB < monitor com 100% sRGB < monitor com 125% sRGB.

---

## Taxa de Atualização

Quadros por segundo enviados ao monitor (Hz = quadros/s).

- **Mínimo**: 60 Hz → abaixo disso ocorre **cintilação** (flickering); causada pela frequência da rede elétrica (60 Hz no Brasil, 50 Hz na Europa → mínimo 50 Hz)
- Maior taxa = menor tempo entre frames = melhor para jogos e movimento rápido
- 60 Hz → 1 frame a cada 16,6 ms; 120 Hz → 1 frame a cada 8,3 ms

> Frequência vertical = taxa de atualização. Frequência horizontal = linhas varridas por segundo.  
> Limite prático = menor entre placa de vídeo, conexão e monitor.  
> Notação: `1920 × 1080 @ 75 Hz`.

---

## Subamostragem de Cores (Chroma Subsampling)

Compressão baseada na maior sensibilidade do olho humano a variações de **brilho** (luma, Y') do que de **cor** (croma).

Formato **J:a:b** em área de J × 2 pixels (J=4 normalmente):
- `a` = amostras de croma na 1ª linha
- `b` = variações de croma entre 1ª e 2ª linha

| Padrão | Compressão | Redução de largura de banda | Qualidade |
|---|---|---|---|
| **4:4:4** | Nenhuma | 0% | Máxima |
| **4:2:2** | Croma ÷ 2 horizontalmente | −1/3 | Boa |
| **4:2:0** | Croma ÷ 4 total | −1/2 | Menor |

### Cálculo de largura de banda sem subamostragem

`largura de banda = colunas × linhas × profundidade_bits × Hz`

Exemplo: 1920 × 1080 × 24 bits × 60 Hz ≈ **3 Gbit/s**  
Exemplo: 3840 × 2160 × 24 bits × 60 Hz ≈ **12 Gbit/s**

> Se a conexão de vídeo não suporta a largura de banda necessária, usar subamostragem reduz o requisito.  
> 4:2:2 e 4:2:0 aceitáveis para vídeo/TV; perceptível em texto e gráficos do PC.

---

## Ver também

- [[Placa de Vídeo]] — gera o sinal de vídeo com os parâmetros descritos aqui
- [[Monitores de Vídeo]] — exibe pixels com resolução nativa, taxa de atualização e profundidade de cores
- [[Conectores de Vídeo]] — largura de banda limita resolução/taxa/profundidade suportadas
