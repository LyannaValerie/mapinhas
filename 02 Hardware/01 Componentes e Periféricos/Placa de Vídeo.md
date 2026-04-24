---
title: Placa de Vídeo
aliases:
  - GPU
  - placa gráfica
  - processador gráfico
  - VRAM
  - memória de vídeo
  - RAMDAC
  - LVDS
  - UMA
  - GPGPU
  - CUDA
  - DirectX
  - NVLink
  - frame buffer
  - aceleração 3D
tags:
  - computação/fundamentos
  - computação/vídeo
  - computação/hardware
date: 2026-04-12
source: "Livro Hardware clubedohardware.com.br — Cap. 42"
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Hardware]]

# Placa de Vídeo

Converte informações digitais geradas pelo processador em sinal para o monitor de vídeo. O processador gráfico (GPU) executa tarefas que seriam do processador principal, aumentando o desempenho.

> [!warning] Terminologia correta
> - "Placa de vídeo" = placa física completa (GPU + VRAM + VRM + conectores)
> - **GPU** = processador gráfico (chip), não a placa inteira — mesmo erro que chamar o computador de "CPU"
> - **Discreta / avulsa** = placa de vídeo separada (não "off-board")
> - **Integrada** = GPU dentro do processador (não "on-board")
> - **VGA** = padrão antigo de resolução/conector; não é sinônimo de placa de vídeo

## Relações (SPO)

- Placa de vídeo discreta → conecta-se via → PCIe x16 ou x8
- GPU integrada → usa → UMA (RAM principal como memória de vídeo)
- GPU → executa → aceleração 2D, aceleração de mídia, aceleração 3D
- GPGPU → usa → GPU para processamento de uso geral (não-gráfico)
- Frame buffer → armazena → conteúdo atual da tela na VRAM
- RAMDAC → converte → VRAM digital → sinal analógico (VGA)
- LVDS → transmite → sinal digital aos conectores de vídeo
- NVLink → conecta → duas GPUs NVIDIA para GPGPU/multi-GPU topo de linha

---

## Cenários de Uso

| Cenário | GPU | Memória de vídeo | Conector de vídeo |
|---|---|---|---|
| Desktop com placa discreta | Chip dedicado na placa | VRAM dedicada na placa | Na placa de vídeo (não na placa-mãe) |
| Desktop/portátil com GPU integrada | Dentro do processador principal | RAM principal (UMA) | Na placa-mãe |
| Portátil com chip gráfico dedicado | Chip soldado na placa-mãe | VRAM soldada na placa-mãe | Na placa-mãe |

> UMA (Unified Memory Architecture): parte da RAM principal é "roubada" como VRAM. Desempenho inferior à VRAM dedicada.

---

## Arquitetura (Placa Discreta)

```
+12 V → VRM → GPU
              ├── Barramento PCIe x16/x8 ↔ CPU
              ├── Barramento da memória de vídeo ↔ VRAM
              ├── RAMDAC → conector analógico (VGA, obsoleto)
              └── LVDS → conectores digitais (HDMI, DisplayPort, etc.)
```

### Interface de Comunicação (CPU ↔ GPU)

PCIe **x16** (padrão) ou x8. Na GPU integrada: barramento interno ao processador.

### Processador Gráfico (GPU)

- Centenas ou milhares de unidades de processamento (núcleos/motores gráficos; "núcleos CUDA" na NVIDIA)
- Clock variável (Dynamic Clock — análogo ao [[Processador#Clock e Desempenho|Clock Dinâmico]])
- Uso específico: gráficos (padrão) ou uso geral via GPGPU
- Pode incluir decodificador/codificador de mídia integrado
- Fabricantes: AMD, NVIDIA, Intel (retornou em 2022)

### Memória de Vídeo (VRAM)

Armazena: conteúdo da tela (frame buffer) + texturas de jogos + cache interno da GPU.

**Frame buffer:**
```
frame buffer (bytes) = (colunas × linhas × bits_de_cor) ÷ 8
```
Exemplo: 1920 × 1080 × 32 bits ÷ 8 = **8,3 MiB** por frame.

**Double/Triple buffering:** VRAM dividida em bancos; GPU preenche próximo quadro enquanto monitor exibe o atual.

**Mitos sobre VRAM:**
- Mais VRAM ≠ mais desempenho; só importa quando texturas necessárias excedem a capacidade
- Tecnologia mais nova ≠ mais rápida; o que importa é a **largura de banda** do barramento
- VRAM (GDDR5/6) não tem nenhuma relação com RAM principal (DDR4/5) — incompatibilidade não faz sentido

### Barramento da Memória de Vídeo

Largura de banda = bits do barramento × clock da memória.

Exemplo: 256 bits × 5 GHz = 160 GB/s < 128 bits × 14 GHz = 224 GB/s  
→ barramento de 128 bits pode ser mais rápido que 256 bits dependendo do clock.

### RAMDAC

Lê VRAM e converte para sinal **analógico** (usado por conector VGA, obsoleto).  
Clock mínimo para gerar uma resolução:
```
clock = colunas × linhas × taxa_de_atualização × 1,4   (fator retraço)
```
Exemplo: 1024×768 @ 75 Hz → RAMDAC ≥ 82,5 MHz.  
Dual RAMDAC = dois sinais analógicos independentes.

### LVDS

Sinal digital diferencial de baixa tensão para as saídas digitais. Gerado diretamente pela GPU.

### Circuito Regulador de Tensão

Mesmo princípio do [[Circuito Regulador de Tensão]] da placa-mãe. Formato `a+b`:
- `a` = fases para o chip gráfico (maior quantidade)
- `b` = fases para os chips de memória

---

## Conectores de Alimentação Auxiliar (PEG)

Slot PCIe x16 fornece no máximo **75 W**. Para mais potência: conector(es) auxiliares.

| Conector | Potência |
|---|---|
| Sem auxiliar (slot apenas) | 75 W |
| 6 pinos | 75 W (+75 W slot = 150 W total) |
| 8 pinos | 150 W (+75 W slot = 225 W total) |
| 6 + 6 pinos | 225 W total |
| 6 + 8 pinos | 300 W total |
| 8 + 8 pinos | 375 W total |
| 8 + 8 + 8 pinos | 525 W total |
| 12 pinos NVIDIA 18 AWG | 500 W + slot = 575 W |
| 12 pinos NVIDIA 16 AWG | 600 W + slot = 675 W |
| 12VHPWR 150 W | 225 W total |
| 12VHPWR 300 W | 375 W total |
| 12VHPWR 450 W | 525 W total |
| 12VHPWR 600 W | 675 W total |

**12VHPWR** (12+4 pinos, ATX 3.0 / Intel 2022): 4 pinos extras comunicam potência máxima requerida à fonte.  
Conector proprietário NVIDIA 12 pinos: mecanicamente compatível com 12VHPWR.  
AWG: menor número = fio mais grosso = maior capacidade de corrente.

---

## Modos de Operação

| Modo | Descrição | Uso atual |
|---|---|---|
| **Texto** | Tela dividida em matriz (ex: 80×25); cada célula = 1 char na VRAM | POST, Linux console, DOS |
| **Gráfico** | Cada pixel individualmente endereçável na VRAM | Todos os SOs modernos |

---

## Aceleração

### 2D
GPU substitui CPU para desenhar primitivas (retângulos, linhas etc.) na VRAM.  
API: **Direct2D** (atual), DirectDraw (obsoleto).

### Mídia
Decodificação e codificação de vídeo por hardware (presente em GPUs discretas e integradas).  
Reduz carga da CPU. Detalhes no Cap. 43.

### 3D
GPU gera imagens tridimensionais (eixo z) em tempo real para jogos e CAD.  
Processo computacionalmente intensivo; detalhes no Cap. 44.

---

## Interface de Programação (API)

Camada intermediária entre jogo/programa e GPU — elimina dependência do conjunto de instruções específico do hardware.

```
Programa → API → GPU
```

| API | Criador | Uso principal |
|---|---|---|
| **DirectX** | Microsoft | Jogos Windows |
| **OpenGL** | Khronos Group | Multiplataforma |
| **Vulkan** | Khronos Group | Multiplataforma, baixo nível |
| **OpenCL** | Khronos Group | GPGPU multiplataforma |
| **DirectCompute** | Microsoft (parte do DirectX) | GPGPU Windows |

**DirectX vinculado ao Windows** (a partir do Vista):

| Versão DirectX | Sistema Operacional mínimo |
|---|---|
| 8.1 | Windows XP |
| 9.0c | Windows XP (SP2/SP3) |
| 10 | Windows Vista |
| 11 | Windows 7 |
| 12 | Windows 10 / 11 |

> GPU DirectX 12 em Windows 8 = jogo DX12 não roda. Sem solução — limitação de hardware+SO.

---

## GPGPU

Uso da GPU para processamento de uso geral (não-gráfico). Alta em: operações repetitivas sobre grandes volumes de dados.

- **CUDA** (NVIDIA): proprietário; só GPUs NVIDIA
- **OpenCL / DirectCompute**: abertos; todas as marcas

Aplicações: supercomputadores / HPC, inteligência artificial, renderização, Photoshop/Premiere, mineração de criptomoedas.

**Placas sem saída de vídeo** (GPGPU puro):
- NVIDIA: Tesla → NVIDIA Datacenter GPUs (2020+)
- AMD: FireStream → FirePro → Radeon Instinct

### LHR e CMP (NVIDIA)
- **LHR** (Lite Hash Rate): trava antimineração em GPUs topo de linha para consumidores
- **CMP** (Cryptocurrency Mining Processor): versão sem LHR e sem conectores de vídeo, preço mais elevado

---

## Multi-GPU

| Tecnologia | Fabricante | Status |
|---|---|---|
| CrossFire | AMD | Descontinuado 2017 |
| SLI | NVIDIA | Descontinuado 2020 |
| Multi-GPU nativo | DirectX 12 | Ativo; requer suporte explícito no jogo (raros) |
| **NVLink** | NVIDIA | Ativo; apenas topo de linha + GPGPU |

**NVLink** — largura de banda por par de fios (modo simplex duplo):

| Versão | Largura de banda/par |
|---|---|
| 1.0 | 20 Gbit/s |
| 2.0 | 25 Gbit/s |
| 3.0 | 50 Gbit/s |
| 4.0 | 50 Gbit/s |

---

## Múltiplos Monitores

- Cada monitor requer conector individual (exceto DisplayPort: daisy-chain por uma porta)
- Monitores não precisam ser iguais nem ter mesma resolução
- Placas adicionais podem ampliar o número de monitores; não precisam ser do mesmo fabricante
- Quantidade suportada depende do modelo da GPU (tipicamente 2–6)

---

## Ver também

- [[Princípios de Vídeo]] — pixel, resolução, taxa de atualização, profundidade de cores, frame buffer
- [[Aceleração de Mídia]] — decodificação/codificação de vídeo por hardware; melhoria de imagem
- [[Aceleração 3D]] — pipeline rasterização; ray tracing; AA; AF; V-Sync; DLSS/FSR
- [[Slots de Expansão]] — PCIe x16/x8 como interface da placa de vídeo; conectores de alimentação PEG
- [[Circuito Regulador de Tensão]] — VRM da placa de vídeo; formato a+b (GPU + memória)
- [[RAM — Tecnologias e Módulos]] — GDDR/HBM como memória de vídeo; UMA usa DDR da RAM principal
- [[Placa-mãe]] — conectores de vídeo ativos só com iGPU; GPU integrada usa RAM via UMA
- [[Conectores de Vídeo]] — VGA, DVI, HDMI, DisplayPort: tipos de saída física da placa
