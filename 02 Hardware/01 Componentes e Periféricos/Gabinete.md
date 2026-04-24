---
title: Gabinete
aliases:
  - case
  - torre
  - mid tower
  - full tower
  - SFF
  - HTPC
  - SECC
  - baia de armazenamento
  - hot swap gabinete
tags:
  - computação/fundamentos
  - computação/hardware
date: 2026-04-12
source: "Livro Hardware clubedohardware.com.br — Cap. 52"
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Hardware]]

# Gabinete

Estrutura física que aloja os componentes do computador.

> [!note] Terminologia
> Chamar o gabinete de "CPU" está errado — CPU é sinônimo de processador. Gabinete = case.

---

## Formatos

| Formato | Placa-mãe suportada | Slots | Características |
|---|---|---|---|
| **Mid Tower** | ATX e menores | 7 (alguns: 4) | Padrão para usuários comuns |
| **Full Tower** | ATX e menores | 7–8+ | Mais baias; melhor refrigeração; pode ter 2 fontes |
| **Compacto (SFF)** | microATX ou menor | Variável | Pouco espaço; portabilidade; circulação de ar inferior; limitações de cooler, GPU e fonte |
| **HTPC** | ATX ou microATX | 7 ou 4 | Estilo home theater; alguns: LCD touch, controle remoto |

---

## Compatibilidade — Pontos a Verificar

| Item | O que verificar |
|---|---|
| **Formato da placa-mãe** | XL-ATX, E-ATX, ATX, microATX, Mini-ITX etc. |
| **Slots de expansão** | 7 (torre), 4 (microATX), 8+ (full tower) |
| **Comprimento da placa de vídeo** | Distância entre slots e baias de armazenamento; algumas gaiolas são removíveis |
| **Fonte de alimentação** | Profundidade; SFX12V/Flex ATX em compactos; picoPSU externo |
| **Altura do cooler** | Restrição crítica em gabinetes compactos |
| **Radiador de refrigeração líquida** | Espaço para radiador + ventoinhas |
| **Unidade óptica** | 5,25" (padrão) ou slim (compactos/HTPC) |
| **Baias de armazenamento** | Quantidade e tamanhos disponíveis |

---

## Material

| Material | Características |
|---|---|
| **SECC** (aço revestido de zinco) | Mais comum e barato |
| **Alumínio** | Mais leve; melhor dissipação de calor; mais caro |
| **Híbrido aço/alumínio** | Aço na estrutura interna/laterais; alumínio no painel frontal |
| **Acrílico** | Totalmente transparente; caro, pesado, má dissipação de calor → ventoinhas extras necessárias |

---

## Baias de Armazenamento

### Tipos de Baia

| Baia | Uso | Status |
|---|---|---|
| Externa 5,25" | Ópticas, controladores de ventoinha, leitores de cartão | Em desuso |
| Externa slim | Ópticas slim | Compactos/HTPC |
| Externa 3,5" | Disquetes (obsoleto), controladores de ventoinha, leitores de cartão | Rara |
| Interna 3,5" | HDs desktop | Padrão; mid tower tem ≥3 |
| Interna 2,5" | SSDs e HDs notebook | Várias localizações possíveis |
| Base externa hot swap | HDs/SSDs 3,5" e 2,5" | Remove/instala sem cabos; remoção com PC ligado (se placa-mãe suportar) |

### Baias Internas 3,5" — Detalhes
- Orientação padrão ou rotacionadas 90° (facilita instalação)
- Gaiola removível: melhora fluxo de ar e acomoda GPUs longas
- Adaptadores: convertem baia 5,25" ou externa 3,5" em interna 3,5"

### Baias 2,5" — Localizações Possíveis
- Dentro de baia 3,5"/2,5" combinada (melhor opção)
- Gaiola dedicada
- Painel inferior
- Atrás da bandeja da placa-mãe
- Sobre divisória do compartimento da fonte

### Tampas das Baias Externas
- **Sólidas**: padrão
- **Perfuradas + filtro de ar**: melhor fluxo de ar; evita entrada de poeira

---

## Sistemas de Instalação Sem Parafusos

| Tipo de baia | Mecanismo |
|---|---|
| 5,25" | Peça/botão de pressão; trilhos laterais deslizantes |
| 3,5" | Gavetas; parafusos especiais de deslizamento; trilhos laterais |

---

## Conectores do Painel Frontal/Superior

| Conector | Cor/Versão |
|---|---|
| USB 2.0 | Preto; conector tipo A |
| USB 3.0 (USB 3.2 Gen 1x1) | Azul; conector tipo A |
| USB 3.1 (USB 3.2 Gen 2x1) | Azul-claro ou vermelho; conector tipo C |
| Saída para fones de ouvido | P2 / 3,5 mm |
| Entrada de microfone | P2 / 3,5 mm |

---

## Localização da Fonte de Alimentação

| Posição | Ventoinha voltada para | Vantagem | Desvantagem |
|---|---|---|---|
| **Superior** (tradicional) | Compartimento interno | Exaustão integrada do gabinete | Fonte opera mais quente |
| **Inferior** (atual) | Para baixo (painel inferior) | Fonte mais fria → mais eficiente, mais silenciosa, maior vida útil | Gabinete precisa de ventoinhas de exaustão próprias |

Posição inferior com ventoinha para baixo: muitos gabinetes incluem filtro de ar no painel inferior para a ventoinha da fonte.

---

## Ventoinhas

- Locais e tamanhos variam por modelo
- LED colorido fixo ou **RGB** configurável por software/controle remoto
- Botão para desligar LEDs (útil em quarto de dormir)
- Controle de velocidade: 2 níveis, 3 níveis ou potenciômetro contínuo
- Painel esquerdo com ventoinha: alguns modelos têm conexão rápida que desconecta automaticamente ao remover o painel

---

## Filtros de Ar

- Instalados nas ventoinhas de ventilação (entrada de ar)
- Alguns gabinetes: filtro no painel superior (exaustão) — impede entrada de objetos
- Remoção periódica necessária para limpeza
- Reduz levemente o fluxo de ar; compensa pelo interior mais limpo
- Melhor opção: removíveis pelo lado de fora do gabinete

---

## Aberturas na Bandeja da Placa-Mãe

| Abertura | Função |
|---|---|
| Acesso ao cooler | Troca de cooler sem remover placa-mãe; auxílio em refrigeração líquida |
| Passagem de cabos | Cabos passam por trás da bandeja → interior mais organizado → melhor fluxo de ar |

---

## Slots de Expansão

- 7 em torres; 4 em microATX; 8+ em full towers
- Tampas sólidas (padrão) ou perfuradas (melhor fluxo de ar)
- Modelos baratos: tampas descartáveis. Melhores: tampas reaproveitáveis
- Fixação de placas: parafusos convencionais ou parafusos de dedo

---

## Outros Recursos

| Recurso | Descrição |
|---|---|
| Parafusos de dedo nos painéis laterais | Dispensa chave de fenda; alguns usam alavancas (sem parafusos) |
| Local para lacre/cadeado | Impede abertura por pessoas não autorizadas |
| Antifurto de periféricos | Prede cabos de teclado, mouse e headset; parafuso acessível só por dentro |
| Pés ajustáveis | Elevam gabinete; alguns escamoteáveis ou com rodas |
| Forração acústica | Espuma/plástico nos painéis para reduzir ruído |
| Suspensão anti-vibração | Anéis de borracha para HDs; reduz propagação de vibração para a estrutura |
| Furos para mangueiras | Suporte a sistemas de refrigeração líquida com componentes externos |

---

## Relações (SPO)

- Gabinete → aloja → [[Placa-mãe]], [[Placa de Vídeo]], [[Disco Rígido]], [[SSD]], fonte de alimentação
- Formato do gabinete → determina → formato máximo da placa-mãe compatível
- Gaiola removível → permite → GPUs longas e melhor fluxo de ar
- Fonte na parte inferior com ventoinha para baixo → opera → mais fria e silenciosa
- Hot swap → permite → instalação/remoção de armazenamento sem desligar o PC (se placa-mãe suportar)
- Filtro de ar → reduz → poeira interna; reduz levemente fluxo de ar

---

## Ver também

- [[Placa-mãe]] — formatos compatíveis (ATX, microATX, Mini-ITX); conectores internos do gabinete
- [[Slots de Expansão]] — quantidade de slots determina compatibilidade com gabinete
- [[Disco Rígido]] — baias 3,5"; hot swap; vibração
- [[SSD]] — baias 2,5"; M.2 dispensam baia
- [[Portas de Comunicação]] — USB, P2: presentes no painel frontal do gabinete
- [[Ventoinha]] — posicionamento, pressão de ar, tipos de rolamento
- [[Cooler]] — radiadores AIO substituem ventoinhas no gabinete
- [[Fonte de Alimentação]] — posicionamento no gabinete; fan-up vs fan-down; exaustão
