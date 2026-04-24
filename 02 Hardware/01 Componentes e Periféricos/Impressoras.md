---
title: Impressoras
aliases:
  - impressora laser
  - impressora jato de tinta
  - inkjet
  - laser printer
  - toner
  - tambor fotossensível
  - PostScript
  - CMYK
tags:
  - computação/hardware
date: 2026-04-17
source: "Organização Estruturada de Computadores — Tanenbaum, Cap. 2"
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Hardware]]

# Impressoras

Periféricos de saída que produzem cópias físicas de documentos. Dois tipos dominantes: **laser** e **jato de tinta**.

## Relações (SPO)

- Impressora laser → usa → tambor fotossensível + toner + fuser
- Impressora jato de tinta → usa → cabeça de impressão com bocais (térmicos ou piezoelétricos)
- PostScript → é → linguagem de descrição de página interpretada pela impressora
- GDI → transfere → renderização para o processador do computador (host-based)
- CMYK → é → modelo de cores subtrativo usado em impressão física

---

## Impressora Laser

### Funcionamento

```
[Tambor fotossensível]
       ↓
[Corona wire carrega tambor com alta tensão (+)]
       ↓
[Laser descarrega áreas selecionadas (forma a imagem)]
       ↓
[Toner (pó negativamente carregado) adere às áreas descarregadas]
       ↓
[Papel passa sobre o tambor → toner transferido ao papel]
       ↓
[Fuser (calor ~200°C + pressão) derrete toner no papel]
```

| Componente | Função |
|---|---|
| **Tambor fotossensível** | Carregado eletrostaticamente; laser apaga seletivamente |
| **Corona wire** | Fio de tungstênio que carrega o tambor com alta tensão |
| **Toner** | Pó plástico pigmentado; adere onde o tambor foi descarregado |
| **Fuser** | Rolo aquecido que fixa o toner no papel por fusão |
| **Laser** | Aponta via espelhos rotativos; descarrega áreas que receberão tinta |

### Linguagem de Página

**PostScript** (Adobe, 1984): linguagem interpretada. A impressora contém processador próprio e RAM; interpreta o programa PostScript e renderiza a página internamente. Independente da resolução — ideal para profissionais.

**GDI (Windows) / host-based**: o computador renderiza a página como bitmap e envia os pontos prontos. Impressora mais barata (sem processador próprio), mas sobrecarrega o computador.

### Características
- Resolução típica: 600–1200 dpi (dots per inch)
- Velocidade: 10–80 ppm (páginas por minuto)
- Alta qualidade de texto; custo por página baixo em volume
- Toner dura milhares de páginas

---

## Impressora Jato de Tinta (Inkjet)

Projeta gotículas minúsculas de tinta líquida diretamente no papel através de bocais.

### Dois Mecanismos

**Térmico (Bubble Jet — Canon/HP):**
1. Resistor aquece a ~500°C em microssegundos
2. Tinta vaporiza → bolha de vapor se expande
3. Bolha força gotícula para fora do bocal
4. Bolha colapsa → pressão negativa suga nova tinta para o bocal

**Piezoelétrico (Epson):**
1. Cristal piezoelétrico deforma sob campo elétrico
2. Deformação mecânica cria pressão no reservatório de tinta
3. Pressão expele gotícula pelo bocal
4. Não usa calor → menos degradação da tinta; maior controle sobre gotícula

| Aspecto | Térmico | Piezoelétrico |
|---|---|---|
| Mecanismo | Resistor + vapor | Cristal + pressão mecânica |
| Calor | Sim (desgasta tinta) | Não |
| Controle de gotícula | Limitado | Maior precisão |
| Fabricantes | Canon, HP | Epson |
| Custo de fabricação | Mais barato | Mais caro |

### Características
- Resolução típica: 600–5760 dpi
- Velocidade menor que laser
- Custo por página maior; tinta cara
- Excelente para fotos e gradientes

---

## Modelo de Cores CMYK

Impressão usa cores **subtrativas** — cada pigmento absorve (subtrai) certas frequências da luz refletida.

| Canal | Cor | Absorve |
|---|---|---|
| C | Ciano | Vermelho |
| M | Magenta | Verde |
| Y | Amarelo | Azul |
| K | Preto (Key) | Toda a luz visível |

> [!note] Por que K separado?
> CMY misturados deveriam produzir preto, mas na prática geram marrom escuro. Tinta preta separada (K) é mais econômica e produz texto mais nítido.

---

## Ver também

- [[Monitores de Vídeo]] — saída visual; RGB (aditivo) vs. CMYK (subtrativo)
- [[Portas de Comunicação]] — USB como interface padrão de impressora moderna
- [[Placa-mãe]] — porta paralela (LPT) e porta serial (COM) usadas historicamente
- [[Mouse]] — outro periférico de E/S conectado via USB