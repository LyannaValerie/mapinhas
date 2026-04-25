---
title: Prevenção de Impasses
aliases:
  - prevenção de deadlocks
  - deadlock prevention
tags:
  - computação/sistemas
  - sistemas-operacionais/impasses
source: "Sistemas Operacionais Modernos, cap. 6"
---

# Prevenção de Impasses

Estratégia que **nega estruturalmente** ao menos uma das [[Condições de Coffman]], tornando impasses impossíveis por projeto. Diferente de [[Evitar Impasses]] (decisões em tempo de execução).

## Atacar cada condição — §6.6

### 1. Atacar exclusão mútua — §6.6.1
"Se nunca acontecer de um recurso ser alocado exclusivamente para um único processo, jamais teremos impasses."
- Dados somente leitura: processos usam simultaneamente.
- **Spooling de impressora**: o único processo que realmente solicita a impressora física é o daemon de impressão. "Dado que o daemon jamais solicita quaisquer outros recursos, podemos eliminar o impasse para a impressora."
- Princípio geral: "Evitar alocar um recurso a não ser que seja absolutamente necessário, e tentar certificar-se de que o menor número possível de processos possa, realmente, requisitar o recurso."
- Cuidado: se o daemon começar a imprimir antes de todo o spool estar completo e dois processos preencherem metade do espaço de spool, surge impasse no disco.

### 2. Atacar posse e espera — §6.6.2
"Exigir que todos os processos solicitem todos os seus recursos antes de iniciar a execução."
- Se tudo disponível → processo executa até o fim.
- Se algum recurso ocupado → nada alocado, processo espera.
- Problema 1: muitos processos não sabem de quantos recursos precisarão ao iniciar.
- Problema 2: recursos não usados de maneira otimizada (ficam alocados ociosos).
- Variante: processo libera temporariamente todos os recursos ao solicitar um novo; tenta obter todos de uma só vez.

### 3. Atacar não preempção — §6.6.3
Virtualizar recursos que normalmente seriam exclusivos.
- Spooling de saída da impressora no disco: apenas daemon acessa a impressora real. Elimina impasse na impressora, mas cria potencial de impasse sobre espaço em disco (com disco grande, improvável).
- Limitação: "nem todos os recursos podem ser virtualizados dessa forma. Por exemplo, registros em bancos de dados ou tabelas dentro do sistema operacional devem ser travados para serem usados."

### 4. Atacar espera circular — §6.6.4
Fornecer **numeração global de todos os recursos**. Regra: processos só podem solicitar recursos em ordem crescente de numeração.
- Se processo possui recurso n, só pode pedir recurso m > n.
- Elimina espera circular matematicamente (provável por argumento de contradição).
- Alternativa simples mas restritiva: processo tem direito a apenas um recurso de cada vez.
- Dificuldade: encontrar numeração conveniente que caiba bem na prática do sistema.

## Relações (SPO)

- Prevenção de Impasses -> quebra -> [[Condições de Coffman]]
- Prevenção de Impasses -> elimina estruturalmente -> [[Impasse]]
- spooling -> elimina -> exclusão mútua na impressora
- ordem total de recursos -> elimina -> espera circular
- Prevenção de Impasses -> contrasta com -> [[Evitar Impasses]]
- Prevenção de Impasses -> contrasta com -> [[Detecção de Impasses]]

