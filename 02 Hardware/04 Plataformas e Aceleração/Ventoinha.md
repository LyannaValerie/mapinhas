---
title: Ventoinha
aliases:
  - Fan
  - Case Fan
  - Cooler Fan
tags:
  - hardware
  - refrigeração
  - ventoinha
date: 2026-04-12
source: "Clube do Hardware - Cap. 53"
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Hardware]]

# Ventoinha

Dispositivo responsável por movimentar ar dentro do gabinete e sobre dissipadores. Opera em conjunto com [[Cooler]] e [[Gabinete]] para controle térmico do sistema.

## Posicionamento e Fluxo de Ar

### Posições no Gabinete

| Painel       | Modo     | Função                          |
| ------------ | -------- | ------------------------------- |
| Frontal      | Entrada  | Ventilação (ar frio entra)      |
| Traseiro     | Saída    | Exaustão (ar quente sai)        |
| Inferior     | Entrada  | Ventilação (ar frio sobe)       |
| Superior     | Saída    | Exaustão (ar quente sobe)       |
| Lateral      | Entrada  | Ventilação direta para GPU      |

### Pressão de Ar

| Tipo     | Definição                        | Efeito                              |
| -------- | -------------------------------- | ----------------------------------- |
| Positiva | Mais entrada do que saída        | Menos poeira; ar sai por frestas    |
| Negativa | Mais saída do que entrada        | Mais poeira; ar entra por frestas   |

> [!tip] Pressão positiva recomendada
> Reduz acúmulo de poeira em componentes. Ideal combinar com filtros nas entradas.

### PSU e Posicionamento

Configuração tradicional: PSU no topo com ventoinha expelindo calor para fora. Com PSU inferior:

| Configuração      | Ventoinha aponta | Resultado                                  |
| ----------------- | ---------------- | ------------------------------------------ |
| Fan up (↑)        | Para dentro      | PSU puxa ar quente do gabinete — pior      |
| Fan down (↓)      | Para fora/chão   | PSU puxa ar frio de baixo — melhor         |

Fan down = PSU mais fria, maior vida útil, menor ruído.

## Características Técnicas

| Parâmetro    | Unidade | Observação                                                    |
| ------------ | ------- | ------------------------------------------------------------- |
| Tamanho      | mm      | 80 a 230 mm; maior = mais fluxo em menor RPM = menor ruído   |
| Velocidade   | RPM     | Rotações por minuto                                           |
| Fluxo de ar  | CFM     | Cubic feet per minute; volume deslocado                       |
| Ruído        | dB / dBA | Alguns fabricantes usam Sone como alternativa                |

## Estrutura Física

**Rotor**: pás, eixo, ímã  
**Estator**: rolamentos, bobinas, frame

## Tipos de Rolamento

| Tipo              | Vida útil (h) | Ruído  | Restrição              | Mecanismo                              |
| ----------------- | ------------- | ------ | ---------------------- | -------------------------------------- |
| Sleeve (bucha)    | ~40.000       | Baixo  | Deve ser vertical      | Óleo entre eixo e bucha; óleo resseca  |
| Ball (esfera)     | ~63.000       | Alto   | Nenhuma                | 2 rolamentos de esfera (ou 1S1B)       |
| Rifle             | Intermediário | Médio  | Nenhuma                | Sleeve + 1 ranhura helicoidal          |
| FDB / Hidrodinâmico | ~100.000+  | Baixo  | Nenhuma                | Sleeve + múltiplas ranhuras; eixo flutua em óleo |

> [!note] FDB
> Fluid Dynamic Bearing. Eixo completamente suspenso em filme de óleo. Combina silêncio do sleeve com durabilidade do ball.

## Iluminação

- LED cor fixa
- RGB: requer controlador — pode vir da placa-mãe, GPU ou controlador do gabinete

## Conectores e Fios

### Número de Fios

| Pinos | Funções                         |
| ----- | ------------------------------- |
| 2     | Alimentação (VCC + GND)         |
| 3     | Alimentação + tacômetro         |
| 4     | Alimentação + tacômetro + PWM   |

PWM (4 pinos) permite controle preciso de velocidade via sinal digital.

### Tipos de Conector de Instalação

| Conector           | Origem         | Uso                          |
| ------------------ | -------------- | ----------------------------|
| Peripheral power   | Direto da PSU  | Ventoinhas de gabinete       |
| 3-pin mobo         | Placa-mãe      | Ventoinhas de gabinete       |
| 4-pin mobo (CPU)   | Placa-mãe      | Cooler de CPU, bomba d'água  |

---

## Relações

| Sujeito     | Predicado        | Objeto               |
| ----------- | ---------------- | -------------------- |
| Ventoinha   | compõe           | [[Cooler]]           |
| Ventoinha   | instalada em     | [[Gabinete]]         |
| FDB         | evolução de      | Sleeve bearing       |
| PWM 4-pin   | controla         | velocidade da ventoinha |
| Pressão positiva | reduz       | acúmulo de poeira    |
| CFM         | mede             | fluxo de ar          |

---

## Ver também

- [[Cooler]] — dissipação de calor do processador
- [[Gabinete]] — posicionamento e fluxo de ar no case
