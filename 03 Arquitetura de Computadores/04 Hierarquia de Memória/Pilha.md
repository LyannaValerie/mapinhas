---
title: Pilha
aliases:
  - stack
  - LIFO
  - pilha de chamadas
  - call stack
  - stack pointer
  - SP
tags:
  - computação/arquitetura
  - computação/fundamentos
date: 2026-04-09
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Pilha

## Definição
Região da [[Memória|RAM]] acessada pelo [[Processador]] como memória temporária de uso interno, com comportamento **LIFO** (Last In, First Out — o último dado inserido é o primeiro a sair).

> [!example] Analogia
> Pense em uma pilha de folhas de papel na mesa: a folha colocada no topo é sempre a primeira a ser removida. Não é possível retirar uma folha do meio sem antes remover todas as que estão acima.

## Relações (SPO)
- Pilha → armazenada em → [[Memória]] (RAM)
- Pilha → controlada por → Stack Pointer (SP)
- Pilha → opera com → instruções PUSH e POP
- Pilha → usada por → instrução CALL para guardar endereço de retorno
- Stack Pointer → é → [[Registrador]] especial dentro do [[Processador]]

## Operações

| Instrução | Ação | Efeito no Stack Pointer |
|---|---|---|
| **PUSH** | Insere dado no topo da pilha | SP decrementado (aponta endereço menor) |
| **POP** | Retira dado do topo da pilha | SP incrementado (aponta endereço maior) |

> [!note] Direção de crescimento na memória
> Na convenção x86, a pilha **cresce em direção aos endereços menores**: cada PUSH move o SP "para cima" na pilha (endereço menor); cada POP move "para baixo" (endereço maior). Endereços crescem de baixo para cima na representação visual, mas a pilha cresce no sentido oposto.

### Stack Pointer (SP)
[[Registrador]] especial que armazena o **endereço do topo atual da pilha**. O processador usa SP para saber onde ler/escrever a cada PUSH ou POP.

## Exemplo — sequência PUSH/POP

```
Estado inicial: pilha vazia

PUSH 10  → pilha: [10]          SP aponta para 10
PUSH 20  → pilha: [10, 20]      SP aponta para 20
PUSH 30  → pilha: [10, 20, 30]  SP aponta para 30

POP      → retorna 30, pilha: [10, 20]
POP      → retorna 20, pilha: [10]
POP      → retorna 10, pilha: vazia
```

## Uso principal — controle de sub-rotinas (CALL/RET)

O uso fundamental da pilha é o **desvio e retorno de sub-rotinas**:

1. **CALL** (x86) / **JSR** (outras arquiteturas): desvia a execução para a sub-rotina e **armazena automaticamente o endereço de retorno na pilha** (PUSH implícito)
2. A sub-rotina executa normalmente
3. **RET** (x86) / **RTS** (outras arquiteturas): faz POP automático para recuperar o endereço de retorno e retomar a execução no ponto anterior

> [!tip] Sub-rotinas aninhadas
> Mesmo que uma sub-rotina chame outras sub-rotinas (aninhamento profundo), a pilha mantém todos os endereços de retorno em ordem LIFO. A cada instrução de retorno, o processador volta ao ponto exato anterior — sem se "perder", independentemente da profundidade do aninhamento.

> [!note] Terminologia x86
> Na arquitetura x86, sub-rotinas são chamadas **procedures** (procedimentos).

## Quadro de Pilha (Stack Frame)

Cada chamada de procedimento cria um **quadro** no topo da pilha. O quadro contém:

```
┌─────────────────┐  ← SP (topo)
│ variáveis locais│
├─────────────────┤  ← FP (Frame Pointer / EBP)
│   FP antigo     │
├─────────────────┤
│ endereço retorno│  ← empilhado pelo CALL
├─────────────────┤
│  parâmetros     │  ← empilhados pelo chamador
└─────────────────┘
```

- **FP / EBP (Frame Pointer / ponteiro de quadro):** aponta para posição fixa dentro do quadro; permite acessar parâmetros e variáveis locais com deslocamentos constantes, mesmo que SP flutue
- **Prólogo:** código executado na entrada do procedimento — salva FP antigo, copia SP→FP, avança SP para alocar variáveis locais
- **Epílogo:** código na saída — restaura SP e FP ao estado anterior, retorna via RET

> [!info] Instrução ENTER/LEAVE no Core i7
> `ENTER SIZE, LEVEL` executa o prólogo; `LEAVE` executa o epílogo. Foram projetadas para tornar chamadas de procedimento mais eficientes — mas só funcionam se o compilador usar o mesmo modelo de frame.

### Procedimentos Recursivos e a Pilha

Cada chamada recursiva empilha um **novo quadro independente** com seus próprios parâmetros e variáveis locais. A pilha cresce enquanto a recursão aprofunda e encolhe no retorno.

> [!example] Torres de Hanói
> `torres(3, 1, 3)` gera 7 chamadas recursivas. Em cada chamada, um novo quadro é criado com os valores de `n`, `i`, `j` e `k` específicos daquela invocação. Os valores de chamadas anteriores permanecem preservados na pilha até o respectivo retorno.
>
> ```
> torres(3,1,3) → torres(2,1,2) → torres(1,1,3) → imprime, retorna
>                                ← torna ao frame de torres(2,1,2)
>              → torres(1,1,2) → ...
>              → torres(2,2,3) → ...
> ```
>
> A recursão funciona porque cada quadro tem sua própria cópia dos parâmetros e variáveis — não há risco de uma chamada sobrescrever dados de outra.

---

## Notação Polonesa Invertida e Endereçamento de Pilha

**Notação infixa** (convencional): operador entre os operandos → `(A + B) × C`

**Notação polonesa invertida (RPN / pós-fixa):** operador após os operandos → `A B + C ×`

### Vantagens da RPN
1. Sem parênteses — nenhuma expressão exige parênteses, independentemente da complexidade
2. Operadores aparecem na **ordem de execução real**
3. Sem regras de precedência arbitrárias

### Algoritmo de avaliação via pilha

Percorrer a expressão RPN da esquerda para a direita:
- **Operando:** empilha
- **Operador:** desempilha dois valores, aplica a operação, empilha o resultado

> [!example] Avaliação de `(8 + 2 × 5) / (1 + 3 × 2 − 4)` em RPN: `8 2 5 × + 1 3 2 × + 4 − /`
>
> | Passo | Símbolo | Pilha |
> |---|---|---|
> | 1 | 8 | 8 |
> | 2 | 2 | 8, 2 |
> | 3 | 5 | 8, 2, 5 |
> | 4 | × | 8, 10 |
> | 5 | + | 18 |
> | 6 | 1 | 18, 1 |
> | 7 | 3 | 18, 1, 3 |
> | 8 | 2 | 18, 1, 3, 2 |
> | 9 | × | 18, 1, 6 |
> | 10 | + | 18, 7 |
> | 11 | 4 | 18, 7, 4 |
> | 12 | − | 18, 3 |
> | 13 | / | **6** |
>
> Resultado: **6**. Uma instrução por símbolo — geração de código trivial para compiladores.

### Geração de código para pilha
Para compilar `A B + C ×`:
1. Cada variável/constante → instrução de push (`BIPUSH`, `ILOAD`)
2. Cada operador → instrução correspondente (`IADD`, `IMUL`)

Isso é a base do bytecode da JVM e da IJVM estudada anteriormente.

---

## Corrotinas

**Procedimento normal:** chamador inicia; chamado sempre começa do início; retorno sempre vai para a instrução seguinte ao `CALL`.

**Corrotina:** dois procedimentos A e B que se chamam mutuamente como pares. Quando A "chama" B, a execução de B **continua do ponto onde parou da última vez** — não do início.

```
A ──► B (continua de onde parou)
B ──► A (continua de onde parou)
```

- Cada transferência é bidirecional: salva o estado de onde "chamou" e retoma o estado do outro
- Implementação: instrução que permuta o topo da pilha com o PC (rara no hardware — normalmente simulada)
- **Uso:** simular processamento paralelo em uma única CPU; cada corrotina executa em pseudoparalelismo

> [!note] Instrução CALL vs. transferência de corrotina
> `CALL` empilha endereço de retorno e desvia. `RET` desempilha e retorna. Para corrotinas, é necessário simultaneamente: (1) empilhar o PC atual como "ponto de retorno" e (2) carregar o PC salvo da outra corrotina — equivalente a permutar topo da pilha com PC.

---

## Ver também
- [[Registrador]] — Stack Pointer (SP) como registrador especial
- [[Memória]] — a pilha reside fisicamente na RAM
- [[Processador]] — instruções CALL/RET fazem parte do ISA x86
- [[Linguagem Assembly]] — uso direto de PUSH/POP em assembly
- [[ISA]]
- [[Modos de Endereçamento#Endereçamento de Pilha]]
- [[Interrupção]] — interrupções salvam contexto na pilha
