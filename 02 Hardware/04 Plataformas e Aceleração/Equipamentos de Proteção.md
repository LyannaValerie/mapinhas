---
title: Equipamentos de Proteção
aliases:
  - Nobreak
  - UPS
  - Estabilizador de Tensão
  - Filtro de Linha
  - Protetor de Surto
tags:
  - hardware
  - alimentação
  - proteção-elétrica
date: 2026-04-12
source: "Clube do Hardware - Cap. 59"
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Hardware]]

# Equipamentos de Proteção

Três categorias: estabilizadores de tensão, filtros de linha e nobreaks.

---

## Estabilizadores de Tensão

> [!danger] Não usar
> Estabilizadores de tensão vendidos no Brasil **não funcionam** e podem queimar a fonte de alimentação ou causar comportamento errático. Construir um que funcione corretamente custaria centenas de reais — impossível nos preços praticados (< R$100).

**Por que são desnecessários:**
- [[Fonte de Alimentação]] com PFC ativo (full range) aceita 100–240 V automaticamente
- Mesmo fontes antigas devem suportar 90–135 V (rede 115 V) ou 180–265 V (rede 230 V)
- Estabilizadores surgiram para compensar fontes ruins dos anos 90/2000 — contexto superado

**Por que são prejudiciais — o problema do relé:**

Estabilizadores baratos usam relé (chave eletromecânica) para comutar saídas do transformador.

| Fase             | Problema                                                    |
| ---------------- | ----------------------------------------------------------- |
| Comutação        | Contato central fica sem conexão NF nem NA durante o atraso |
| Efeito           | Interrupção momentânea da tensão → computador pode reiniciar/travar |
| Ruído elétrico   | Relés geram ruído ao comutar — piora condições da rede      |

**Hold-up**: fontes ATX devem suportar 17 ms sem tensão (capacitores do primário). Se o relé atrasar mais que isso + a fonte for ruim → problema garantido.

**Conclusão:** melhor comprar fonte de boa qualidade (PFC ativo) do que fonte ruim + estabilizador.

---

## Filtros de Linha

Removem ruídos e picos da rede elétrica. Também chamados: **protetores contra surtos de tensão**.

> [!warning] Qualidade importa
> Muitas "réguas" vendidas não têm componentes eletrônicos — são apenas tomadas em paralelo. Fontes de boa qualidade já incluem filtragem de transientes internamente.

**Quando vale a pena:** proteção contra raios — mesmo indiretos (indução em fios da rede pode gerar milhares de V). PSU sozinha não protege contra raios. Bom filtro de linha protege mesmo com computador desligado.

---

## Nobreaks

**Nome correto em inglês: UPS** (Uninterruptible Power Supply). "Nobreak" = inglês inventado no Brasil.

**Função:** manter computador ligado durante queda de energia para salvar trabalho e desligar corretamente. Não é para uso prolongado (modelo residencial).

**Conectar ao nobreak:** computador + monitor + modem + roteador. **Não conectar:** impressora laser.

### Capacidade: VA vs W

$$\text{Potência (W)} = \text{Potência (VA)} \times FP$$

Fabricantes rotulam em VA com FP baixo (0,50) para inflar capacidade. Calcular sempre em W.

| FP declarado | Nobreak 1 kVA = ? W |
| ------------ | ------------------- |
| 0,50         | 500 W               |
| 0,60         | 600 W               |
| 0,90         | 900 W               |

**Consumo do computador:**

$$\text{Consumo total} = \frac{\text{Consumo do computador}}{\text{Eficiência da PSU}}$$

Somar consumo de monitor e demais periféricos. Capacidade do nobreak > consumo total + margem.

### Tipos de Nobreak

#### Off-line (Short-break)

Mais barato e mais comum.

**Operação normal (rede presente):**
- Saída = tensão da rede passando por estabilizador (baseado em relé — mesmos problemas)
- Bateria carrega em paralelo

**Operação em bateria (queda de energia):**
- Circuito inversor converte CC da bateria em CA
- Forma de onda: quadrada ou pseudo-senoidal (não senoidal real)
- Comutação via relé → atraso na troca (pode afetar PSUs ruins)

**Defeitos:**
1. Estabilizador interno com relé
2. Forma de onda não senoidal
3. Atraso de comutação

#### Off-line Senoidal Pura

Off-line melhorado: gera senoidal real quando na bateria. Ainda usa relé na comutação — atraso persiste.

#### On-line (Dupla Conversão)

Melhor tipo. Sem pontos negativos além do preço.

**Princípio:** bateria **sempre** conectada à saída via inversor, independente da rede.

| Aspecto           | Off-line             | On-line                    |
| ----------------- | -------------------- | -------------------------- |
| Atraso comutação  | Sim (relé)           | Zero (bateria sempre ativa) |
| Forma de onda     | Quadrada / pseudo    | Senoidal pura o tempo todo |
| Qualidade saída   | Depende da rede      | Gerada do zero, limpa      |
| Custo             | Baixo                | Alto                       |

> [!tip] On-line
> Saída senoidal pura gerada sempre pelo inversor — melhor que a própria rede elétrica. Nenhum atraso em caso de queda.

### Bateria do Nobreak

Usa [[Bateria]] do tipo **chumbo-ácido VRLA** (SLA). Autonomia residencial: suficiente para salvar e desligar o computador.

---

## Relações

| Sujeito            | Predicado          | Objeto                          |
| ------------------ | ------------------ | ------------------------------- |
| Estabilizador      | prejudica          | [[Fonte de Alimentação]] ruim   |
| Fonte full range   | torna desnecessário | Estabilizador de tensão        |
| Nobreak on-line    | elimina            | atraso de comutação             |
| Nobreak off-line   | usa                | relé (atraso + ruído)           |
| Filtro de linha    | protege contra     | raios (picos de tensão)         |
| Nobreak            | usa bateria        | [[Bateria]] VRLA chumbo-ácido   |
| Hold-up            | especificado em    | 17 ms mínimo (ATX)              |
| VA → W             | requer             | fator de potência               |

---

## Ver também

- [[Fonte de Alimentação]] — PFC ativo; full range; hold-up; filtragem de transientes
- [[Bateria]] — tecnologias; VRLA; autonomia mAh/Wh
