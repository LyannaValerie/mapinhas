---
title: Tipos de Computadores
aliases:
  - categorias de computadores
  - zoológico dos computadores
tags:
  - computação/fundamentos
date: 2026-04-06
---

# Tipos de Computadores

## Taxonomia atual

| Tipo | Preço (aprox.) | Exemplos de aplicação |
|---|---|---|
| **Computador descartável** | ~$0,50 | Cartões de felicitação, chips RFID em embalagens |
| **[[Microcontrolador]]** | ~$5 | Relógios, carros, eletrodomésticos, computadores embutidos |
| **Computador móvel / jogos** | ~$50 | Smartphones, videogames domésticos |
| **Computador pessoal** | ~$500 | Desktop, [[Notebook]] |
| **Servidor** | ~$5.000 | Servidor de rede, serviços web |
| **Mainframe** | ~$5.000.000 | Processamento em lote em bancos, grandes empresas |

> [!info] Origem da diversidade
> A [[Lei de Moore]] permite que o mesmo avanço em transistores seja usado de formas opostas: construir máquinas mais poderosas pelo mesmo preço *ou* construir o mesmo desempenho por menos dinheiro. A indústria fez ambas — daí a ampla variedade de categorias.

## Computadores descartáveis

Categoria mais surpreendente: chips de uso único embutidos em produtos de baixo custo.

### RFID (Radio Frequency IDentification)

| Característica | Passivo | Ativo |
|---|---|---|
| Bateria | Não — alimentado pelo sinal externo | Sim |
| Complexidade | Mínima — só transmite ID | Pode conter processador e fazer cálculos |
| Exemplos | Etiquetas de produtos, documentos | Smart cards financeiros |

**Especificações básicas:**
- Tamanho: <0,5 mm de espessura; custo: centavos
- Contém transponder de rádio + número único de **128 bits**
- Baixa frequência: maior alcance, menor taxa de transferência
- Alta frequência: menor alcance, maior taxa de transferência

**Diferencial em relação ao código de barras:** o código de barras identifica o *tipo* de produto; o RFID identifica o *item específico* (cada embalagem tem ID único).

**Aplicações:**
- Checkout automático em lojas; antifurto; controle de estoque
- Rastreamento de recall: identificar lotes defeituosos mesmo distribuídos globalmente
- Pedágio eletrônico (E-Z Pass); rastreamento de bagagens aéreas
- Chips integrados a cédulas de euro (rastreamento e invalidação de dinheiro ilícito)
- Rastreamento de animais, rebanhos; monitoramento ambiental (temperatura, pressão, umidade)

> [!warning] Implicações de privacidade
> Com 128 bits únicos por item, sistemas RFID permitem rastrear indivíduos através de produtos, cartões e veículos. Discussão pública sobre privacidade é inevitável.

## Microcontrolador

Ver [[Microcontrolador]] para detalhes completos.

Computador completo em chip único, embutido em dispositivos não vendidos como computadores. Restrições: custo mínimo, resposta em tempo real, tamanho físico reduzido.

## Computadores móveis e de jogos

### Consoles de jogos
Computadores otimizados para jogos 3D e multimídia. Sistemas fechados — sem expansão de hardware pelo usuário. Desempenho comparável ou superior a PCs em aspectos específicos.

| Console | CPU | RAM | Diferencial |
|---|---|---|---|
| Sony PlayStation 3 | Cell 3,2 GHz (multicore RISC PowerPC + 7 SPEs de 128 bits) | 512 MB | SPE 128 bits; Blu-ray |
| Microsoft Xbox 360 | PowerPC triple-core 3,2 GHz | 512 MB | GPU ATI; DVD |
| Samsung Galaxy Tablet | Dual-core ARM 1 GHz (Nvidia Tegra 2) | 1 GB | Câmeras; giroscópio; memória flash |

### Computadores móveis (smartphones, tablets)
Requisito adicional crítico: **consumo mínimo de energia** (vida de bateria). Desafio: usuários esperam alto desempenho (gráficos 3D, vídeo HD) com energia limitada.

## Servidor

PC reforçado ou estação de trabalho. Arquitetura similar a um PC de alto nível — diferencia-se em velocidade, memória, capacidade de disco e rede. Executa Unix ou Windows. Pode ter múltiplos processadores e manipular milhares de transações/segundo.

**Clusters e Data Centers:**
- **Cluster:** múltiplos servidores COTS conectados por redes de gigabits/s; operam como uma máquina para processamento paralelo
- **Data center:** sala/prédio que abriga o cluster; escala de dezenas a milhares de servidores
- Principal uso: servidores web (distribuição de milhares de requisições/segundo entre os nós)

Ver [[Computação em Nuvem]] para o impacto dos data centers no paradigma de computação.

## Tablet

Variante do PC: embalagem menor, **disco de estado sólido** (sem disco giratório), **tela sensível ao toque**, CPU não-x86 (geralmente ARM). Arquitetonicamente equivalente a um notebook em formato diferente.

## Mainframe

Computador de grande porte voltado para processamento em massa de dados. Descendente direto do IBM 360. Diferencia-se do servidor principalmente em capacidade de **E/S** e volume de dados, não tanto em velocidade de CPU.

- Mantidos em operação por inércia: enorme investimento em software legado (décadas), dados, procedimentos e pessoal
- Reavivados pela Internet: poderosos servidores de transações de e-commerce
- O problema do "Ano 2000" originou-se em código Cobol dos anos 1960/70 que representava o ano com 2 dígitos

## Supercomputador

Máquinas com CPUs extremamente velozes, muita RAM e redes muito rápidas. Usados para cálculos científicos massivos (simulação de galáxias, síntese de medicamentos, modelagem aerodinâmica).

> [!warning] Categoria em extinção
> Data centers construídos com componentes comerciais passaram a oferecer potência equivalente por preços muito menores. Supercomputadores tradicionais estão sendo substituídos por clusters de servidores COTS.

## Observação de Hamming

> "Uma mudança de uma ordem de grandeza em quantidade causa uma mudança na qualidade."
> — Richard Hamming (Bell Labs)

Um computador $500 e um mainframe de $5M não são o mesmo produto em escalas diferentes — são categorias qualitativamente distintas.

## Ver também
- [[Lei de Moore]]
- [[Microcontrolador]]
- [[PC]]
- [[Notebook]]
- [[Computador]]
- [[Computação em Nuvem]]
- [[História da Computação]]
