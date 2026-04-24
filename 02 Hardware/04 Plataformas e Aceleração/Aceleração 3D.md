---
title: Aceleração 3D
aliases:
  - rasterização
  - ray tracing
  - traçado de raios
  - pipeline gráfico
  - anti-aliasing
  - MSAA
  - SSAA
  - FSAA
  - DLSS
  - FSR
  - RSR
  - V-Sync
  - filtragem anisotrópica
  - wireframe
  - z-buffer
  - ROP
  - TMU
  - BVH
  - sombreamento
  - fps
  - frame rate
  - tesselação
tags:
  - computação/fundamentos
  - computação/vídeo
date: 2026-04-12
source: "Livro Hardware clubedohardware.com.br — Cap. 44"
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Hardware]]

# Aceleração 3D

Uso da GPU para calcular e renderizar imagens tridimensionais em tempo real, em vez da CPU.

## Relações (SPO)

- Aceleração 3D → usa → GPU no lugar da CPU para cálculo e renderização 3D
- Rasterização → converte → vetores 3D em pixels (técnica padrão, tempo real)
- Ray tracing → simula → comportamento físico da luz (alta qualidade, alto custo)
- Pipeline de rasterização → executa → etapas sequenciais da geração de imagem
- Z-buffer → armazena → profundidade relativa de cada vértice/fragmento
- ROP → grava → fragmentos finais na VRAM; executa fusão com z-buffer
- TMU → aplica → texturas nos fragmentos
- BVH → acelera → detecção de colisão de raios com triângulos no ray tracing
- Desempenho → medido em → fps (frames per second)

---

## Eras dos Jogos

| Era | Técnica | Profundidade | Exemplos |
|---|---|---|---|
| 2D (até início 1990s) | Mapeamento de bits | Apenas x/y | Super Mario, Mortal Kombat |
| 2.5D (~1990s) | Bitmap com perspectiva | x/y/z simulado | Wolfenstein 3D, Doom |
| 3D genuíno (1996+) | Vetorial + aceleração 3D | x/y/z real | Quake (marco: 1996) |

> **GeForce 256 (1999)**: primeiro chip a executar cálculo geométrico na GPU — NVIDIA cunhou o termo "GPU" (Graphics Processing Unit).  
> Tecnicamente: GPU = chip gráfico com cálculo geométrico integrado. Hoje todos os chips para PC têm cálculo geométrico → termos são intercambiáveis.

---

## Qualidade × Desempenho

- Desempenho medido em **fps** (frames per second)
- Mínimo aceitável: **30 fps** (33 ms/frame); ideal: **60 fps** (16 ms/frame)
- Mais resolução + mais efeitos = mais processamento = menos fps
- Desempenho em jogos: **quase exclusivamente ditado pela GPU** (exceto GPUs de entrada)
- CPU fica ociosa na maior parte do tempo em jogos com GPU de alto desempenho

---

## Rasterização

Técnica padrão para renderização em tempo real.

### Pipeline

```
1. Envio de Instruções          ← CPU envia geometria e comandos à GPU
2. Sombreamento de Vértices     ← vértices → coordenadas 2D + z-buffer (profundidade)
3. Tesselação (opcional)        ← divide triângulos em triângulos menores (DX11 / OpenGL 4.0+)
4. Sombreamento de Geometria    ← adiciona/move vértices (DX10 / OpenGL 3.2+, opcional)
5. Montagem de Primitivas       ← une vértices em triângulos; clipping + face culling
6. Rasterização                 ← primitivas vetoriais → fragmentos (pixels)
7. Sombreamento de Pixels       ← cor/textura por pixel; TMU aplica texturas
8. Fusão e Saída (ROP)          ← z-buffer descarta pixels ocultos; grava na VRAM
```

### Detalhes das Etapas

**Sombreamento de Vértices**: converte vértices para coordenadas 2D; calcula z-buffer.  
**Tesselação**: subdivide triângulos automaticamente → maior detalhe sem o jogo precisar enviar mais triângulos. Três substágios: sombreamento de casco / tesselação / sombreamento de domínio (DX) ou TCS / gerador / TES (OpenGL).  
**Montagem de Primitivas**: cria triângulos; **clipping** descarta fora da tela; **face culling** descarta faces não visíveis → ganho de desempenho.  
**Rasterização**: seleção por ponto central — pixel selecionado apenas se centro estiver dentro da primitiva → serrilhado (aliasing).  
**Sombreamento de Pixels** (= sombreamento de fragmentos): processa cada pixel do fragmento; cor e textura.  
**Fusão (ROP)**: usa z-buffer para descartar pixels cobertos por outros fragmentos; grava resultado final na VRAM. **Taxa de preenchimento** (fill rate) = taxa de transferência da ROP.

> A partir do DX10: unidades de shader unificadas — mesmas unidades processam vértices, geometria e pixels.

---

## Traçado de Raios (Ray Tracing)

Simula física real da luz: lança raios por pixel em direção à cena tridimensional (mantida no formato 3D, diferente da rasterização).

### Tipos de Raios

| Raio | Função |
|---|---|
| **Visualização** | Do pixel → objeto mais próximo; captura cor base |
| **Sombra** | Do ponto de acerto → fonte de luz; verifica oclusão |
| **Refração** | Atravessa objeto translúcido; cor do fundo/objeto atrás |
| **Reflexão** | Reflete do objeto; cor de objetos na direção do reflexo |

Cada raio adicional = mais realismo + mais custo computacional.  
Cinema: milhares de raios/pixel → horas/dias por frame.  
Jogos: dezenas de raios/pixel; requer suporte no jogo E no chip gráfico (a partir de 2018: NVIDIA RTX, depois AMD).

**Desempenho mínimo (NVIDIA):** 5 bilhões de raios/segundo para boa qualidade de iluminação.

### Algoritmo BVH (Bounding Volume Hierarchy)

Acelera detecção de colisão raio-triângulo. Divide cena em caixas aninhadas.  
**Raio de travessia**: verifica quais caixas o raio atinge → descarta o restante.  
**Raio de interseção**: verifica colisão com triângulos dentro da caixa encontrada.

### Pipeline de Ray Tracing

```
Geração de Raios → Agendamento → Travessia/Interseção* → Agendamento → Sombreamento → (loop)
```
*Etapas marcadas executadas pela **unidade de ray tracing** dedicada do chip.  
Pode retornar para gerar mais raios após sombreamento (loop).

**Sombreadores específicos do ray tracing:**
- Geração de raios: inicia o traçado; controla todos os demais
- Interseção: define como raios atingem a geometria
- Erro: raio não atingiu geometria
- Acerto mais próximo: sombreamento do ponto de interseção; pode gerar novos raios
- Qualquer acerto: executado para cada acerto (ex: transparência)

### Híbrido

Rasterização para a maior parte da cena + ray tracing apenas em objetos específicos → equilíbrio desempenho/qualidade.

---

## Ajustes de Qualidade

### Suavização de Serrilhado (Anti-Aliasing / AA)

**Problema:** vetores → pixels = borda irregular ("efeito escada") por discretização.

| Técnica | Método | Impacto desempenho | Disponibilidade |
|---|---|---|---|
| **SSAA / FSAA** | Renderiza em resolução maior; reduz para resolução final (2x, 4x, 8x) | Altíssimo | Todos; VSR (AMD), DSR/DLDSR (NVIDIA) via driver |
| **MSAA** | Múltiplos pontos de amostragem só nas bordas das primitivas | Moderado | Todos |
| **CSAA** (NVIDIA) / **EQAA** (AMD) | MSAA + pontos de cobertura (não de cor); maior cobertura sem impacto significativo | Baixo extra vs. MSAA | NVIDIA GeForce 8+ / AMD HD 6900+ |
| **MFAA** | MSAA com pontos amostrados em quadros alternados | Menor que MSAA | Apenas NVIDIA GeForce 9+ |
| **MLAA** | Pós-processamento; detecta e desfoca quebras de continuidade | Mínimo | Apenas AMD |
| **FXAA** | Pós-processamento; detecta contraste abrupto e desfoca | Muito baixo | Apenas NVIDIA (2009) |
| **SMAA** | Pós-processamento; detecta linhas/curvas/bordas (menos embaçado que FXAA) | Baixo | Ambos; combinável com TAA |
| **TAA** | Temporal: funde pontos de quadros adjacentes; reduz serrilhado em movimento | Baixo | Ambos (pode embaçar texturas) |
| **TXAA** (NVIDIA) | MSAA + TAA combinados | Moderado | Apenas NVIDIA |

> Técnicas de pós-processamento (MLAA, FXAA, SMAA, TAA): praticamente sem custo, mas tendem a embaçar a imagem.

### Filtragem Anisotrópica (AF)

**Problema:** mapeamento MIP usa imagens sempre quadradas (isotrópicas) → distorção em superfícies oblíquas ao ponto de vista.  
**Solução:** AF ajusta relação de aspecto das amostras MIP para a perspectiva real → corrige distorção.  
Configuração (2x, 4x, 8x, 16x) = ângulo máximo de correção. Quanto maior = melhor qualidade e menor desempenho.

### V-Sync (Sincronismo Vertical)

**Problema:** GPU grava frames na VRAM mais rápido que o monitor lê → **screen tearing** (quadros cortados/corrompidos na tela).  
**V-Sync:** limita fps da GPU ao valor da taxa de atualização do monitor (ex: 60 Hz → 60 fps máx).  
**Alternativas com menor latência:** G-SYNC (NVIDIA) e FreeSync (AMD) — taxa de atualização variável no monitor (Cap. 46).

---

## Técnicas de Aumento de Desempenho (Subescalonamento)

Renderiza internamente a **resolução menor** → escala para a resolução do monitor via algoritmo.  
Oposto do SSAA (que aumenta resolução para depois reduzir).

| Técnica | Fabricante | Disponibilidade |
|---|---|---|
| **DLSS** (Deep Learning Super Sampling) | NVIDIA | Apenas GPUs NVIDIA compatíveis (RTX 2000+, 2018) |
| **FSR** (FidelityFX Super Resolution) | AMD | AMD + NVIDIA; requer suporte no jogo (2021) |
| **RSR** (Radeon Super Resolution) | AMD | AMD + NVIDIA via driver, sem precisar de suporte no jogo (2022) |

> Existem GPUs NVIDIA que não suportam DLSS mas suportam FSR/RSR.

---

## Ver também

- [[Placa de Vídeo]] — GPU, VRAM, TMU, ROP, frame buffer, APIs (DirectX/OpenGL/Vulkan)
- [[Princípios de Vídeo]] — resolução, taxa de atualização, fps, subamostragem
- [[Aceleração de Mídia]] — decodificação/codificação e melhoria de vídeo (cap. paralelo)
