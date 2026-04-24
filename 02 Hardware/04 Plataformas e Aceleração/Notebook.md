---
title: Notebook
aliases:
  - Laptop
  - Computador Portátil
  - Portátil
tags:
  - hardware
  - portátil
  - computador
date: 2026-04-12
source: "Clube do Hardware - Cap. 60"
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Hardware]]

# Notebook

Computador portátil. Mesma arquitetura de um PC de mesa — diferenças são de formato físico, consumo e integração de componentes.

## Nomenclatura

| Termo      | Uso / Origem                                          |
| ---------- | ----------------------------------------------------- |
| Laptop     | Mais comum nos EUA — "colocado no colo"              |
| Notebook   | Mais comum no Brasil — "tamanho de caderno"           |
| Portátil   | Usado em Portugal                                     |
| "Note"/"Not" | Abreviação brasileira — não faz sentido em inglês   |

Em inglês, "notebook" sem contexto remete a caderno, não a computador.

## Objetivos de Design

| Objetivo              | Impacto                                               |
| --------------------- | ----------------------------------------------------- |
| Menor tamanho físico  | Componentes menores, soldados na placa-mãe            |
| Menor peso            | Menos metal, menos componentes separados              |
| Menor consumo elétrico | Maior autonomia da bateria; menos calor gerado       |
| Menor geração de calor | Refrigeração mais simples; menos ruído; conforto no colo |

---

## Componentes

### Processador

- Normalmente **soldado diretamente na placa-mãe** (sem soquete)
- Versões com soquete existem — objetivo: flexibilidade de configuração pelo fabricante, **não** troca pelo usuário
- Menor consumo, menor TDP e menor tamanho físico que equivalentes desktop

> [!tip] Upgrade de desempenho
> Antes de trocar processador: instalar mais RAM e/ou SSD — maior impacto, muito mais fácil.

### Memória RAM

- Chips podem estar **soldados** na placa-mãe (sem substituição possível — só adicionar em soquetes livres)
- Módulos removíveis: formato **SO-DIMM** (menor que DIMM)

**Acesso aos soquetes:**

| Método          | Dificuldade |
| --------------- | ----------- |
| Tampa inferior removível | Fácil |
| Remoção do teclado | Médio — cuidado com base de alumínio (maleável) e cabo plano frágil |
| Abertura completa | Difícil — levar a técnico experiente |

### Placa-mãe

- **Não padronizada** — proprietária por modelo/fabricante
- Substituição: apenas por modelo idêntico
- Slots de expansão disponíveis: M.2 2280 (SSD) e M.2 2230 (rede sem fio)
- Slots legados em modelos antigos: mSATA e Mini PCI Express (substituídos pelo M.2)

### GPU (Placa de Vídeo)

Duas opções — sem placa física separada:

| Tipo                   | Descrição                                          |
| ---------------------- | -------------------------------------------------- |
| Integrada no processador | iGPU — compartilha RAM do sistema                |
| Dedicada soldada       | GPU + VRAM soldados na placa-mãe — não substituível |

> [!note] Terminologia
> "Placa de vídeo integrada" em notebook é impreciso — não há placa separada. GPU e VRAM estão soldados na mobo.

**GPU externa:** possível via USB-C (mínimo USB 3.1 Gen 2 / USB 3.2 Gen 2x1). Alguns modelos exigem Thunderbolt.

### Tela

- Tudo de [[Monitores de Vídeo]] se aplica
- **Touch screen**: operação por toque (gestos como pinça para zoom)
- **Conversível / 2-em-1**: tela gira 180° → modo tablet; alguns modelos a tela é completamente removível
- **Stylus**: caneta especial para maior precisão — ideal para desenho e anotações

### Armazenamento

- HD portátil: 2,5 polegadas, 5.400 rpm
- SSD: soldado na placa-mãe ou em slot M.2 2280

**Upgrade recomendado:** instalar SSD no slot M.2 (se disponível) mantendo HD para dados; ou substituir HD por SSD 2,5".

Acesso ao disco: mesmos métodos descritos em Memória.

### Placa de Rede Sem Fio

- Presente em todos os notebooks modernos
- Formato: M.2 2230
- Normally combina Wi-Fi + Bluetooth
- Slot pode ser M.2 completo (PCIe + SATA) ou CNVi (Intel — só rede)

### Áudio

- Caixas de som embutidas
- Saída: P2 (3,5 mm) + HDMI
- Alguns modelos: SPDIF via P2 (fibra com adaptador 3,5 mm → Toslink)
- Microfone embutido: mono ou estéreo (2 microfones); normalmente acima/ao lado da tela

### Câmera (Webcam)

- Embutida na tampa superior, acima da tela
- Características principais: resolução e taxa de atualização (ideal: 60 fps)
- Qualidade da lente/sensor mais importante que resolução nominal

### Refrigeração

Sistema personalizado por modelo. Componentes típicos:

| Componente          | Equivalente desktop | Função                                 |
| ------------------- | ------------------- | -------------------------------------- |
| Bloco térmico / placa metálica | Base do cooler | Contato com CPU, GPU, chipset       |
| Câmara de vapor     | Câmara de vapor     | Transferência de calor eficiente       |
| Heatpipes           | Heatpipes           | Levam calor do bloco ao dissipador     |
| Dissipador com aletas | Dissipador        | Troca calor com o ar                   |
| Ventoinha radial (blower) | Ventoinha      | Sopra ar pelo dissipador → exaustão    |

Blower: ar entra axialmente, sai radialmente pelas aletas do dissipador, e depois pela abertura de exaustão lateral.

Com baixa carga → ventoinha desligada → silencioso. Com carga alta (edição/jogos) → ventoinha na máxima → ruído elevado.

### Teclado

- Membrana com mecanismo **tesoura** (scissor) — teclas finas por restrição de espaço
- Ver [[Teclado]]

### Touchpad (Trackpad)

Área sensível ao toque abaixo do teclado. Substitui o mouse. Dois botões (destacados ou ocultos). Suporta gestos (pinça, scroll de 2 dedos, etc.).

### Bateria

Ver [[Bateria]] (tecnologia Li-Ion).

| Configuração    | Troca   |
| --------------- | ------- |
| Removível externa | Fácil  |
| Lacrada interna | Difícil — abertura do equipamento |

### Conectores Externos

Soldados diretamente na placa-mãe. Ver [[Portas de Comunicação]] (cap. 39) e [[Conectores de Vídeo]] (cap. 45).

### Outros Recursos

| Recurso                  | Função                                            |
| ------------------------ | ------------------------------------------------- |
| Leitor de impressões digitais | Biometria — proteção de acesso             |
| Leitor de cartões de memória | Câmeras digitais, SD cards                  |
| Dispositivo antifurto (Kensington) | Orifício para cabo de aço com cadeado |

---

## Comparativo Desktop × Notebook

| Aspecto          | Desktop                        | Notebook                          |
| ---------------- | ------------------------------ | --------------------------------- |
| Processador      | Soquete (removível)            | Soldado (maioria)                 |
| GPU              | Placa separada em slot PCIe    | Soldada na mobo ou iGPU           |
| RAM              | DIMM (removível)               | SO-DIMM ou soldada                |
| Placa-mãe        | Padronizada (ATX, mATX, etc.)  | Proprietária                      |
| Expansão         | Vários slots                   | M.2 2280 + M.2 2230               |
| Refrigeração     | Cooler separado + ventoinhas   | Blower + heatpipes + blocos térmicos integrados |
| Alimentação      | PSU interna                    | Adaptador externo + bateria       |
| Atualização      | Alta flexibilidade             | Limitada a RAM e SSD (maioria)    |

---

## Relações

| Sujeito          | Predicado       | Objeto                         |
| ---------------- | --------------- | ------------------------------ |
| Notebook         | usa             | [[Processador]] de baixo consumo |
| Notebook         | usa             | [[Bateria]] Li-Ion             |
| Notebook         | usa módulos     | SO-DIMM ([[RAM — Tecnologias e Módulos]]) |
| Notebook         | armazena em     | [[SSD]] M.2 / [[Disco Rígido]] 2,5" |
| Blower           | é tipo de       | [[Ventoinha]]                  |
| Refrigeração notebook | usa       | heatpipes + câmara de vapor ([[Cooler]]) |
| Teclado notebook | usa             | mecanismo tesoura ([[Teclado]]) |
| GPU externa      | conecta via     | USB-C / [[Portas de Comunicação|Thunderbolt]] |

---

## Ver também

- [[Processador]] — modelos mobile, TDP baixo
- [[RAM — Tecnologias e Módulos]] — SO-DIMM
- [[SSD]] — upgrade de desempenho; M.2
- [[Disco Rígido]] — 2,5 polegadas, 5400 rpm
- [[Bateria]] — Li-Ion; autonomia; cuidados
- [[Cooler]] — heatpipes, câmara de vapor, bloco térmico
- [[Ventoinha]] — blower/radial
- [[Teclado]] — mecanismo tesoura
- [[Monitores de Vídeo]] — telas de notebook; touch; 2-em-1
- [[Conectores de Vídeo]] — HDMI, USB-C, Thunderbolt em notebooks
- [[Portas de Comunicação]] — conectores externos; Thunderbolt; CNVi
- [[Áudio Digital]] — saída P2, HDMI, SPDIF
- [[Placa-mãe]] — formato proprietário; M.2; CNVi
