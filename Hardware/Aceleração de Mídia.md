---
title: Aceleração de Mídia
aliases:
  - decodificador de vídeo
  - codificador de vídeo
  - QuickSync
  - NVDEC
  - NVENC
  - UVD
  - VCE
  - PureVideo
  - desentrelaçamento
  - deinterlacing
  - telecinagem reversa
  - reverse telecine
  - deblocking
  - denoising
tags:
  - computação/fundamentos
  - computação/vídeo
date: 2026-04-12
source: "Livro Hardware clubedohardware.com.br — Cap. 43"
---

# Aceleração de Mídia

Processamento de decodificação, codificação e melhoria de vídeo realizado por hardware dedicado dentro da GPU (ou iGPU), liberando a CPU para outras tarefas.

## Relações (SPO)

- Processador de mídia → integrado em → GPU discreta e iGPU
- Decodificador → converte → vídeo comprimido → frames exibíveis sem uso da CPU
- Codificador → acelera → geração de arquivos de vídeo (edição/streaming)
- Recursos de melhoria → processam → frames já decodificados para aumentar qualidade

---

## Decodificadores e Codificadores por Fabricante

| Fabricante | Decodificador | Codificador |
|---|---|---|
| AMD | UVD ou GCN | VCE |
| NVIDIA | PureVideo ou NVDEC | NVENC |
| Intel | QuickSync | QuickSync |

> Capacidade depende da versão do processador de mídia. Algumas etapas ainda podem cair para software (CPU) em versões mais antigas.

---

## Recursos de Melhoria de Imagem

### Desentrelaçamento (Deinterlacing)

Remove entrelaçamento de vídeos gravados no formato de varredura entrelaçada para exibição em monitores progressivos.  
Vídeo entrelaçado → cada quadro tem metade das linhas → visível como ruído/linhas na imagem progressiva.  
Desentrelaçamento dobra o número de linhas disponíveis por quadro.  
Ver [[Princípios de Vídeo#Varredura]].

### Telecinagem Reversa (Reverse Telecine / Reverse Pulldown)

**Problema:** filmes são capturados a **24 fps**; TV/DVD usa **30 fps** com varredura entrelaçada (60 campos/s).  
**Telecinagem (processo 2:3):** cada quadro ímpar do filme → 2 campos; cada quadro par → 3 campos.

```
Filme 24 fps → [quadro 1: 2 campos] [quadro 2: 3 campos] → 60 campos/s = 30 fps entrelaçado
```

**Problema para desentrelaçamento padrão:** desentrelaçamento supõe que 1 quadro = 2 campos; na telecinagem 1 quadro é reusado mais de uma vez → resultado incorreto.  
**Solução:** telecinagem reversa detecta o padrão 2:3 e desfaz a conversão para restaurar o material original a 24 fps.

### Escalonamento (Scaling)

Aumenta resolução do vídeo por interpolação para preencher a tela inteira.

### Remoção de Blocos (Deblocking)

Remove/suaviza artefatos de bloco causados por compressão excessiva (ex: JPEG/H.264 com bitrate baixo).

### Remoção de Ruído (Denoising)

Remove ruído geral da imagem.

### Redução de Ruído Mosquito

Remove "fantasma" em torno de objetos causado por compressão excessiva — aparece como halo ao redor de bordas definidas.

### Melhoria de Borda (Edge Enhancement)

Aumenta nitidez das bordas dos objetos.

### Correção de Cor (Color Correction)

Melhora automaticamente a cor do vídeo; especialmente útil em filmes antigos.

---

## Ver também

- [[Placa de Vídeo]] — GPU integra processador de mídia; iGPU usa mesma lógica
- [[Princípios de Vídeo]] — varredura entrelaçada/progressiva; taxa de atualização
