---
title: Teclado
aliases:
  - Keyboard
  - Teclado Mecânico
  - Teclado de Membrana
tags:
  - hardware
  - entrada
  - periférico
date: 2026-04-12
source: "Clube do Hardware - Cap. 55"
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Hardware]]

# Teclado

Principal dispositivo de entrada. Classificado pelo tipo de interruptor usado nas teclas.

## Tipos por Tecnologia de Chave

### Membrana

Membrana plástica única com 3 camadas:
- Superior: contatos
- Intermediária: isolamento
- Inferior: contatos

Pressão fecha contato entre camadas superior e inferior.

**Variantes:**

| Variante         | Teclas           | Tátil | Era / Uso                                    |
| ---------------- | ---------------- | ----- | -------------------------------------------- |
| Plano original   | Sem separação    | Não   | ZX81, Atari 400, Odyssey (fim anos 70/80)    |
| Chiclete         | Destacadas/borracha | Sim | TK85, TK90X, notebooks atuais              |
| Domo de borracha | Plástico separado | Sim  | PC desktop — variante mais comum hoje        |
| Tesoura (scissor) | Finas, notebook | Sim  | Notebooks — mola em formato de tesoura       |

**Desvantagens:**
- Erros ao pressionar muitas teclas simultaneamente
- Erros em sequências rápidas (ver NKRO)
- Resposta tátil insuficiente para datilografia rápida sem olhar

### Mecânico

Interruptor individual por tecla (~104 chaves mínimo). Mais caro, mais preciso, melhor resposta tátil.

**Vantagens:**
- Datilografia acelerada sem olhar e sem erros
- Suporte a múltiplas teclas simultâneas (NKRO real)
- Maior vida útil por tecla

> [!tip] Chaves mecânicas
> Existem vários modelos com características distintas de força de acionamento, resposta tátil e sonora (artigo separado: "Tabela comparativa de chaves de teclados mecânicos").

---

## Acionamento Simultâneo de Teclas

| Conceito       | Definição                                                                  |
| -------------- | -------------------------------------------------------------------------- |
| NKRO / n-key rollover | Todas as teclas reconhecidas independente de quantas pressionadas  |
| x-key rollover | Suporta X teclas simultâneas (ex: 4-key rollover = 4 teclas)               |
| Ghosting (1)   | Tecla fantasma enviada sem ter sido pressionada (raro hoje)                |
| Ghosting (2)   | Incapacidade de reconhecer 3+ teclas por limitação da matriz — problema real |
| Anti-ghosting  | Tecnologia que elimina o ghosting tipo 2                                   |

> [!warning] Limitação USB
> Mesmo com teclado NKRO, USB limita a **6 teclas não-modificadoras + 4 modificadoras** simultâneas.  
> PS/2 não tem essa limitação — preferido por jogadores que precisam de NKRO real.

---

## Layout

| Layout        | Teclas | Diferencial                         |
| ------------- | ------ | ----------------------------------- |
| Norte-americano | 104  | Padrão internacional                |
| ABNT          | 106   | Adiciona tecla "Ç"                  |
| ABNT2         | 107   | Adiciona "Alt Gr"                   |
| Tenkeyless (TKL) | ~87 | Remove teclado numérico à direita   |

Teclas adicionais opcionais: controle de mídia, macros, atalhos de programas.

---

## Material das Teclas

| Material | Qualidade | Uso            |
| -------- | --------- | -------------- |
| ABS      | Padrão    | Maioria dos teclados |
| PBT      | Superior  | Modelos mais caros |
| POM      | Superior  | Modelos mais caros |

**Impressão dos caracteres:**

| Método        | Durabilidade | Detalhe                              |
| ------------- | ------------ | ------------------------------------ |
| Pintura       | Baixa        | Desbota rapidamente                  |
| Laser         | Média-alta   | Gravação na superfície               |
| Dye-sublimated | Alta        | Sublimação — tinta penetra no plástico |
| Double-shot   | Máxima       | Injeção de plástico em 2 cores       |

---

## Iluminação

- Cor única ou RGB configurável por software
- RGB: suporta zonas com cores distintas e efeitos (degradê, animações)

---

## Conexão

| Tipo     | Vantagem                                          | Desvantagem                        |
| -------- | ------------------------------------------------- | ---------------------------------- |
| PS/2     | NKRO real sem limitação USB                       | Ausente em placas-mãe recentes     |
| USB      | Universal; alguns modelos têm hub USB integrado   | Limite de 6+4 teclas simultâneas   |
| Sem fio  | Mobilidade; receptor USB único para teclado+mouse | Requer bateria/pilhas              |
| Bluetooth | Sem receptor dedicado                            | Requer receptor BT no computador   |

---

## Relações

| Sujeito          | Predicado      | Objeto                    |
| ---------------- | -------------- | ------------------------- |
| Teclado mecânico | supera em tátil | Teclado de membrana      |
| NKRO             | depende de     | conexão PS/2 para ilimitado |
| Ghosting         | ocorre em      | Teclado de membrana       |
| Teclado          | conecta via    | [[Portas de Comunicação]] |
| Teclado gamer    | requer         | NKRO + chaves de qualidade |

---

## Ver também

- [[Mouse]] — dispositivo apontador complementar
- [[Portas de Comunicação]] — PS/2, USB, Bluetooth
