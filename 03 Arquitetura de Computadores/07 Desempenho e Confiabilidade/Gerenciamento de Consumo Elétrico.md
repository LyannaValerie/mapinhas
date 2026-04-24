---
title: Gerenciamento de Consumo Elétrico
tags:
  - hardware
  - arquitetura-x86
  - consumo
  - energia
aliases:
  - ACPI
  - SpeedStep
  - Speed Shift
  - Cool'n'Quiet
  - PowerNow!
  - estados C
  - estados P
  - thermal throttling
  - TDP
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Gerenciamento de Consumo Elétrico

Tecnologias para reduzir consumo elétrico e dissipação térmica sem sacrificar desempenho quando não necessário.

---

## ACPI (Advanced Configuration and Power Interface)

Padrão criado por Intel + Microsoft + Toshiba. Suportado pelo Windows via configurações de energia.

### Estados G/S — Computador

| Estado | Nome | Descrição |
|--------|------|-----------|
| **G0/S0** | Ligado | Operação normal. Submodo "ausente" desliga monitor mas mantém sistema ativo. |
| **G1** | Dormindo | Grupo com submodos S1–S4 |
| S1 | Suspenso | Clock do processador desligado; processador continua ligado |
| S2 | Dormindo | Processador desligado; RAM ligada |
| S3 | Dormindo | Similar ao S2 |
| S4 | Hibernando | RAM salva em disco/SSD → computador desliga. Mais lento para acordar. |
| **G2/S5** | Standby | Computador "desligado" mas +5VSB da fonte ativa (maioria dos PCs desligados) |
| **G3** | Mecanicamente desligado | Fonte removida da rede elétrica |

### Estados D — Dispositivos genéricos

| Estado | Significado |
|--------|-------------|
| D0 | Totalmente ligado |
| D1, D2 | Economia de energia (comportamento definido pelo fabricante) |
| D3 | Completamente desligado |

### Estados C — Processador

| Estado | Nome | Descrição |
|--------|------|-----------|
| **C0** | Ligado | Processador totalmente ativo |
| **C1** | Halt | Para de executar instruções |
| **C2** | Stop Clock | Clock desligado |
| **C3** | Sleep | Processador "dorme" — maior economia |
| C4+ | — | Estados adicionais (modelo-dependente); ACPI trata como C3 |

> [!note] Estados C por núcleo
> Em processadores atuais, cada núcleo pode ter seu estado C independente. Estado C global = menor estado C entre todos os núcleos. Exemplo: estado global C3 exige todos os núcleos em C3 ou mais profundo.

### Estados P — Desempenho/clock do processador

- **P0:** desempenho máximo (clock base + tensão nominal)
- **P1, P2, ...:** clock e tensão reduzidos progressivamente
- Quanto maior o número, menor o clock e menor a tensão → maior economia

---

## Fórmulas de Energia e Potência

### Energia dinâmica (por transição de chaveamento)

$$E_{\text{dynamic}} = \frac{1}{2} \cdot C \cdot V^2$$

### Potência dinâmica

$$P_{\text{dynamic}} = \frac{1}{2} \cdot C \cdot V^2 \cdot f_{\text{switched}}$$

### Potência estática (leakage)

$$P_{\text{static}} = I_{\text{static}} \cdot V$$

Significativa em feature sizes ≤16 nm. Principal causa do fim do [[Lei de Moore|escalonamento de Dennard]].

## Relação Potência × Tensão × Frequência (forma simplificada)

$$P = C \cdot V^2 \cdot f$$

- **P** = potência (W) | **C** = capacitância (constante por chip) | **V** = tensão | **f** = frequência (clock)

Implicações:
1. **Reduzir clock** reduz potência linearmente
2. **Reduzir tensão** reduz potência ao quadrado → efeito muito maior

> [!example] Exemplo hipotético
> Processador 3 GHz / 1,2 V / 10 nF → **43,2 W**
> - Clock → 1 GHz (mesma tensão): **14,4 W**
> - Clock → 1 GHz + tensão → 0,8 V: **6,4 W**

> [!tip] Overclock e tensão
> Para overclock acima do clock padrão, entusiastas aumentam a tensão para manter estabilidade — pela mesma equação, consumo e calor sobem drasticamente.

### Como o clock é reduzido

Processadores usam **multiplicação de clock**: clock interno = clock base × multiplicador.  
Exemplo: 3 GHz = 100 MHz × 30.

Reduzir o multiplicador reduz o clock interno. SpeedStep/Cool'n'Quiet mantêm tabela de pares (multiplicador, tensão) para cada estado P.

---

## Tecnologias de Estados P

| Tecnologia | Fabricante | Segmento | Observações |
|-----------|-----------|----------|------------|
| **SpeedStep / EIST** (Enhanced Intel SpeedStep) | Intel | Mobile/desktop | Transição SO-controlada; latência 100–150 ms |
| **Speed Shift** (DBS em servidores) | Intel | Skylake+ | Transição **processador-controlada**; mais estados P; latência 30–35 ms |
| **Cool'n'Quiet** | AMD | Desktop | — |
| **PowerNow!** | AMD | Mobile | — |

> [!note] Speed Shift vs SpeedStep
> Speed Shift move o controle do SO para o próprio processador, permitindo transições mais rápidas e granulares.

---

## Monitoramento Térmico e Thermal Throttling

Processadores modernos têm **sensor de temperatura interno**.

- Ao se aproximar da temperatura máxima → **thermal throttling**: clock reduzido automaticamente via SpeedStep/Speed Shift/Cool'n'Quiet/PowerNow!
- Processador sem refrigeração adequada nunca atinge desempenho máximo
- GPUs atuais têm mecanismo equivalente
- Processadores antigos (sem sensor interno): monitoramento feito pela placa-mãe via sensor no soquete, configurável via BIOS/setup

> [!warning] Implicação prática
> Thermal throttling é silencioso. Um cooler insuficiente não causa travamento imediato — apenas reduz o clock, degradando desempenho sem aviso explícito.

---

## Ver também

- [[Desempenho do Processador]] — clock dinâmico (Turbo Boost/Precision Boost); clock base vs. clock boost
- [[Cache]] — consumo de cache não abordado aqui
- [[Processador]] — TDP como especificação de projeto
- [[Máquina Virtual]] — estados de energia independentes por VM não cobertos aqui
