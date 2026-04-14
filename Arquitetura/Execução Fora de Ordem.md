---
title: Execução Fora de Ordem
tags:
  - arquitetura
  - pipeline
  - desempenho
  - superescalar
aliases:
  - Out-of-Order Execution
  - OoO
  - Execução OoO
---

# Execução Fora de Ordem

Técnica em que o processador executa instruções em ordem diferente da sequência do programa, aproveitando unidades funcionais ociosas enquanto outras instruções aguardam dependências.

## Relações SPO

| Sujeito | Predicado | Objeto |
|---|---|---|
| OoO | resolve | hazards estruturais e parte dos hazards de dados |
| Scoreboard | rastreia | estado de cada instrução em execução |
| Register renaming | elimina | hazards WAR e WAW |
| ROB | garante | commit em ordem (retirement in-order) |
| Execução especulativa | depende de | previsão de desvio + rollback |

## Tipos de Hazards de Dados

| Tipo | Nome | Descrição | Exemplo |
|---|---|---|---|
| **RAW** | Read After Write | instrução lê antes da anterior escrever | `ADD R1,R2,R3` → `MOV R4,R1` |
| **WAR** | Write After Read | instrução escreve antes da anterior ler | `MOV R4,R1` → `ADD R1,R2,R3` |
| **WAW** | Write After Write | duas escritas no mesmo registrador | `ADD R1,...` → `MOV R1,...` |

- **RAW**: dependência real — não pode ser eliminada sem stall ou forwarding.
- **WAR e WAW**: dependências falsas (nome) — eliminadas por **register renaming**.

## Scoreboard

Estrutura de controle que monitora o estado de cada instrução em voo:

```
Por instrução:
- unidade funcional alocada
- registrador destino
- operandos prontos? (bit por fonte)

Por registrador:
- "occupied" bit — há instrução pendente escrevendo neste reg?
```

Fluxo:
1. **Issue**: instrução emitida se unidade disponível e sem hazard estrutural.
2. **Read operands**: aguarda até operandos prontos (sem RAW pendente).
3. **Execute**: executa na unidade funcional.
4. **Write result**: escreve resultado; libera unidade; atualiza scoreboard.

Problema do scoreboard: não resolve WAR/WAW — ainda bloqueia nesses casos.

## Register Renaming

Mapeia registradores arquiteturais (ISA) para registradores físicos de um pool maior.

Exemplo — elimina WAW e WAR:
```
Código original:       Após renaming:
ADD R1, R2, R3         ADD P7,  P2, P3   (R1→P7)
SUB R1, R4, R5    →    SUB P8,  P4, P5   (R1→P8, nova versão)
MOV R6, R1             MOV P9,  P8       (usa versão mais nova de R1)
```

- Pool de registradores físicos >> registradores arquiteturais.
- Tabela de mapeamento (**RAT** — Register Alias Table) traduz nomes.
- Instrução de write cria nova versão; instrução de read consume a versão mais recente.

> [!note] Core i7 Sandy Bridge
> 160 registradores físicos para 16 registradores de inteiro da ISA x86-64. 168 entradas no ROB (Reorder Buffer).

## Reorder Buffer (ROB)

Permite commit in-order mesmo com execução out-of-order:

1. Instruções entram no ROB em ordem do programa.
2. Executam fora de ordem (marcam "done" no ROB quando completas).
3. Commit (retire) só ocorre quando instrução chega ao HEAD do ROB **e** está "done".

Garante:
- estado arquitetural visível permanece consistente
- exceções são precisas (precise exceptions)
- rollback possível em caso de previsão errada

## Execução Especulativa

Combinação de previsão de desvio + execução antes da condição ser conhecida:

1. [[Previsão de Desvio]] prevê caminho.
2. Instruções especulativas executam e entram no ROB marcadas como especulativas.
3. Se previsão correta → instrução eventualmente commita normalmente.
4. Se previsão errada → ROB descarta instruções especulativas; pipeline volta ao estado correto.

### SPECULATIVE-LOAD e Poisoned Bit

Loads especulativos podem carregar dados cujo endereço depende de resultado ainda não calculado:

- Instrução `SPECULATIVE-LOAD` executa com endereço estimado.
- Se resultado é inválido ou exceção ocorreu → resultado marcado com **poisoned bit**.
- Qualquer instrução que consuma valor envenenado propaga o bit.
- Se instrução com poisoned bit tenta commitar → exceção é disparada nesse ponto.

Permite adiantar loads sem comprometer correção.

## Emissão Superescalar

Processadores superescalares emitem **múltiplas instruções por ciclo**:

```
Frontend → Decodifica N instruções/ciclo
         → Renaming
         → Reservation Stations (aguardam operandos)
         → Dispatch para unidades funcionais (OoO)
         → ROB (commit in-order)
```

| Processador | Largura de emissão | ROB | Reg. físicos |
|---|---|---|---|
| Core i7 Sandy Bridge | 4/ciclo | 168 | 160 inteiro |
| Cortex-A9 | 2/ciclo | — | — |

## Limitações e Custos

- Hardware complexo: RAT, ROB, reservation stations, scoreboard.
- Consumo de energia elevado (muitas comparações paralelas).
- Janela de instrução limitada pelo tamanho do ROB.
- Vulnerabilidades de segurança: Spectre/Meltdown exploram execução especulativa (acesso a dados via side-channel antes do commit).

> [!warning] Spectre e Meltdown
> Execução especulativa permite acesso temporário a dados protegidos antes do rollback. O dado não é commitado, mas deixa rastro em cache (side-channel). Mitigações têm custo de desempenho.

## Ver também

- [[Pipeline]] — base sobre a qual OoO é construído
- [[Previsão de Desvio]] — necessária para execução especulativa
- [[Caminho de Dados]] — registradores físicos e datapath
- [[Concorrência e Paralelismo]] — ILP explorado por OoO
- [[ISA]] — registradores arquiteturais (visíveis ao programador) vs. físicos
