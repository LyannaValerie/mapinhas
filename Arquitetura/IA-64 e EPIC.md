---
title: IA-64 e EPIC
aliases:
  - IA-64
  - Itanium
  - Itanium 2
  - EPIC
  - Explicitly Parallel Instruction Computing
  - predicação
  - carga especulativa
tags:
  - computação/arquitetura
  - computação/histórico
date: 2026-04-13
---

# IA-64 e EPIC

## Definição
**IA-64** é uma ISA de 64 bits desenvolvida em conjunto pela Intel e Hewlett-Packard, baseada no modelo **EPIC** (Explicitly Parallel Instruction Computing — Computação com Instruções Explicitamente Paralelas). Primeiro implementada na família **Itanium** (2001) e **Itanium 2**. Representa uma ruptura radical com a [[Intel x86|IA-32]], movendo trabalho do tempo de execução para o tempo de compilação.

## Relações (SPO)
- IA-64 → desenvolvida por → Intel + Hewlett-Packard
- IA-64 → implementada em → Itanium, Itanium 2
- IA-64 → baseia-se em → modelo EPIC
- EPIC → transfere trabalho de → tempo de execução para tempo de compilação
- IA-64 → visa resolver → limitações da ISA [[Intel x86|IA-32]]
- Itanium → adoção → limitada a servidores de alto desempenho

---

## Problemas da IA-32 que Motivaram a IA-64

### 1. ISA CISC com instruções de comprimento variável
- Instruções IA-32 devem ser decompostas em micro-operações RISC durante a execução
- Requer hardware extra (área de chip), tempo e complexidade
- Tecnologia atual favorece ISAs RISC com comprimento fixo e opcode fácil de decodificar

### 2. Arquitetura baseada em memória, não load/store
- Muitas instruções referenciam a memória diretamente (não apenas LDR/STR)
- CPUs crescem em velocidade muito mais rápido que a memória → gargalo cresce
- Arquitetura load/store elimina a maioria das referências desnecessárias

### 3. Conjunto de registradores pequeno e irregular
- Apenas 4–6 registradores de uso geral realmente intercambiáveis no x86
- Compiladores "despejam" variáveis na memória constantemente → referências extras
- Resultado: dependências artificiais e lentidão

### 4. Complexidade da execução fora de ordem
- Poucos registradores → muitas dependências WAR desnecessárias
- Hardware precisa renomear registradores secretamente no buffer de reordenação
- Interrupções precisas exigem retirada em ordem mesmo com execução fora de ordem

### 5. Previsão de desvio crítica
- Pipeline profundo + execução especulativa = flush caro em má previsão
- Taxa de erro baixa ainda causa degradação substancial de desempenho

> [!quote] Analogia histórica
> A situação da IA-32 é comparável à astronomia pré-Copérnico: epiciclos adicionados a epiciclos até o modelo desabar sob o peso de sua própria complexidade.

---

## O Modelo EPIC

Ideia central: o **compilador** decifra quais instruções podem ser executadas em paralelo e codifica isso explicitamente no programa. O hardware executa sem precisar descobrir o paralelismo em tempo de execução.

| Responsabilidade | Core i7 (IA-32) | Itanium 2 (IA-64 / EPIC) |
|---|---|---|
| Detectar paralelismo | Hardware em tempo de execução | Compilador em tempo de compilação |
| Renomear registradores | Hardware (buffer de reordenação) | Compilador (registradores explícitos) |
| Escalonar unidades funcionais | Hardware dinâmico | Compilador estático |
| Quantidade de registradores | 4–6 visíveis ao compilador | 128 reais, todos visíveis |

---

## Registradores do Itanium 2

| Tipo | Quantidade | Notas |
|---|---|---|
| Uso geral de 64 bits | 128 | 32 estáticos + 96 em pilha de registradores (alocação dinâmica por procedimento) |
| Ponto flutuante IEEE | 128 | Não funcionam como pilha |
| Predicados de 1 bit | 64 | Usados para predicação de instruções |
| Desvio | 8 | Endereços de destino de desvio |
| Aplicação (especiais) | 128 | Passagem de parâmetros entre aplicação e SO |

### Pilha de Registradores
Os 96 registradores dinâmicos funcionam como uma janela deslizante:
- Cada procedimento declara quantos registradores precisa — o ponteiro da pilha de registradores avança
- Parâmetros de entrada são visíveis em registradores ao entrar no procedimento
- Não é necessário salvar/restaurar registradores dinâmicos entre chamadas (ao contrário dos 32 estáticos)
- Permite recursão profunda antes de ter que descarregar registradores para a memória

---

## Pacotes de Instrução

Instruções são agrupadas em **pacotes de 128 bits**:

```
[Instrução 0 — 41 bits] [Instrução 1 — 41 bits] [Instrução 2 — 41 bits] [Gabarito — 5 bits]
```

- **Gabarito (5 bits):** informa quais unidades funcionais cada instrução usa e onde estão as fronteiras de grupo
- Um **grupo de instruções** não precisa coincidir com fronteiras de pacote — pode começar/terminar no meio de um pacote
- Dentro de um grupo: sem conflitos, sem dependências RAW/WAW entre si — CPU pode executar em qualquer ordem ou em paralelo

> [!note] Responsabilidade do compilador
> Se o compilador gerar um grupo que viole as regras, o comportamento é indefinido. Para debug, o compilador pode colocar cada instrução em um grupo separado (seguro, mas lento). Para produção, otimiza para paralelismo máximo.

---

## Predicação

Técnica para eliminar desvios condicionais: toda instrução carrega um **predicado de 1 bit** (campo de 6 bits seleciona um dos 64 registradores de predicado).

**Sem predicação:**
```asm
CMP  R1, R2
BNE  L1
MOV  R3, R4     ; parte then
BR   L2
L1: SUB R6, R4, R5  ; parte else
L2:
```

**Com predicação:**
```asm
CMPEQ R1, R2, P4    ; P4=1 se iguais, P5=1 se diferentes (automaticamente)
(P4) ADD R3, R4, R5 ; executa se P4=1
(P5) SUB R6, R4, R5 ; executa se P5=1
```

- Ambas as instruções entram no pipeline
- Na retirada: verifica predicado → se verdadeiro, escreve resultado; se falso, descarta silenciosamente
- Resultado: **bloco básico sem desvio condicional** — sem flush de pipeline por previsão errada

> [!tip] Quando predicação ajuda
> Funciona melhor para if/else com corpos curtos (< ~7 instruções cada lado). Para corpos longos, o custo de executar ambos os lados supera o custo de um desvio ocasional.

---

## Cargas Especulativas

Problema: acessos à memória são lentos (cache miss). Solução: começar a carga **antes** de saber se ela será necessária.

**Funcionamento:**
1. Compilador eleva instrução `LD.S` (load especulativo) para antes do ponto onde o valor é necessário
2. Se a carga falhar (page fault, etc.): em vez de exceção imediata, marca o registrador como **envenenado**
3. No ponto de uso: instrução `CHK.S` verifica se o registrador está envenenado
   - Não envenenado: `CHK.S` age como NOP — sem custo
   - Envenenado: exceção ocorre agora

> [!example]
> ```
> LD.S R1, [R2]     ; carga especulativa — pode acontecer ciclos antes do uso
> ... outras instruções ...
> CHK.S R1, handler ; verifica se carga foi bem-sucedida
> ADD   R3, R1, R4  ; usa R1
> ```

---

## Por que o Itanium Não Venceu

A arquitetura IA-64 é tecnicamente superior à IA-32 em vários aspectos. Apesar disso, a adoção foi mínima — limitada a servidores de alta tecnologia.

**Razões do fracasso comercial:**
1. **Compiladores difíceis:** o modelo EPIC exige compiladores extremamente sofisticados para extrair o paralelismo. Sem bons compiladores, o hardware não tem vantagem
2. **Incompatibilidade:** código x86 não roda nativamente no Itanium (precisa de emulação lenta)
3. **Concorrência:** a AMD lançou x86-64 (EMT-64) — extensão de 64 bits da IA-32. A Intel foi forçada a adotar. Resolveu o problema de espaço de endereços sem mudar o ISA
4. **Multicore venceu:** a solução que a Intel adotou para o desempenho foi multiprocessamento no chip (múltiplos núcleos), não uma nova ISA
5. **Custo de migração:** substituir todo o software existente é inviável economicamente

> [!note] Itanium vs. solução real
> O Itanium foi a resposta "arquiteticamente correta" para os problemas do x86. A resposta que o mercado aceitou foi os multicores, que contornam os problemas com mais transistores. Trabalhar em tempo de compilação é sempre melhor que em tempo de execução — mas só se os compiladores conseguirem fazer esse trabalho.

---

## Ver também
- [[VLIW]] — Very Long Instruction Word; EPIC como variante VLIW para uso geral
- [[Intel x86]]
- [[RISC e CISC]]
- [[ISA]]
- [[Formatos de Instrução]]
- [[Modos de Endereçamento]]
- [[Previsão de Desvio]]
- [[Execução Fora de Ordem]]
- [[Concorrência e Paralelismo]]
