---
title: Linguagem Simbólica — MAPA
aliases:
  - MAPA
  - Micro-linguagem de Anotação para Arquitetura
  - linguagem simbólica
tags:
  - meta/linguagem
  - meta/especificação
date: 2026-04-15
---

# Linguagem Simbólica — MAPA

> [!abstract] O que é
> **MAPA** (Micro-linguagem de Anotação para Arquitetura) é uma linguagem simbólica textual para representar conceitos, relações e estruturas do vault. Importa operadores de lógica clássica, lógica descritiva (DL) e teoria de conjuntos em um subconjunto mínimo, preservando a semântica SPO já usada nas notas.

---

## Objetivos

1. representar relações entre conceitos de forma **linear, textual e parseável**
2. evitar o excesso formal da lógica de predicados (sem quantificadores aninhados)
3. servir como alternativa compacta a diagramas Mermaid longos
4. permitir geração automática de grafos via parser simples

---

## Princípios de design

- **seletividade**: só entra o operador que agrega valor semântico real
- **mapeamento SPO direto**: cada símbolo corresponde a um predicado verbal já em uso nas notas
- **legibilidade humana prioritária**: frases curtas, símbolos reconhecíveis
- **ordem binária**: toda sentença é `termo RELAÇÃO termo`, sem aridade variável
- **sem inferência automática**: a linguagem descreve, não deduz

---

## Núcleo de operadores

| Símbolo | Nome | Origem | Semântica |
|---|---|---|---|
| `⊑` | subtipo | Description Logic | `x ⊑ y` = `x` é tipo ou especialização de `y` |
| `≡` | equivalência | lógica | `x ≡ y` = `x` e `y` são o mesmo conceito |
| `⊗` | composição | custom | `x ⊗ y` = `x` é composto por `y` |
| `⊳` | interface/acesso | custom | `x ⊳ y` = `x` acessa ou usa `y` |
| `→` | sucessão histórica | seta temporal | `x → y` = `y` sucedeu `x` no tempo |
| `⇒` | implicação funcional | lógica | `x ⇒ y` = `x` viabiliza/implica `y` |
| `≺` | nível inferior | ordem | `x ≺ y` = `x` está em nível de abstração abaixo de `y` |
| `∈` | pertinência | conjuntos | `x ∈ C` = `x` pertence à categoria `C` |
| `∧` | conjunção | lógica | junta propriedades de um mesmo termo |
| `∨` | alternativa | lógica | alternativa taxonômica |
| `¬` | negação | lógica | contraste / exclusão |

### Descartados deliberadamente

`∀`, `∃`, `□`, `◇`, lambda, quantificadores aninhados, tipos dependentes.
Motivo: excesso de formalidade sem ganho para notas técnicas descritivas.

---

## Gramática

```
sentença       := termo RELAÇÃO termo [":" qualificador]
termo          := Entidade
                | Entidade "[" param "]"
                | "{" lista_termos "}"
                | "(" sentença ")"
lista_termos   := termo ("," termo)*
RELAÇÃO        := "⊑" | "≡" | "⊗" | "⊳" | "→" | "⇒" | "≺" | "∈"
conectivo      := "∧" | "∨"
qualificador   := ano | nível | contexto livre
Entidade       := identificador (pode conter acento)
```

### Regras de forma

- um bloco `mapa` contém uma sentença por linha
- comentários iniciam com `#`
- wiki-links podem ser usados como termo: `[[Computador]] ⊗ {[[Hardware]], [[Software]]}`
- qualificador separado por `:` descreve contexto (ano, nível, observação curta)

---

## Tabela de tradução SPO → MAPA

| Predicado SPO (PT) | Operador MAPA |
|---|---|
| "é tipo de", "especialização de" | `⊑` |
| "equivale a", "é o mesmo que" | `≡` |
| "composto por", "contém" | `⊗` |
| "acessa", "usa", "opera sobre" | `⊳` |
| "substituiu", "sucedeu" | `→` |
| "viabiliza", "permite", "base para" | `⇒` |
| "nível abaixo de", "interpretado por" | `≺` |
| "pertence a", "classificado como" | `∈` |

---

## Tradução do mapa de `Index.md`

Diagrama Mermaid original do `Index.md` traduzido para MAPA:

```mapa
# Substituição histórica
ComputadorProgramaFixo → ModeloVonNeumann
EDVAC ⇒ ModeloVonNeumann                    : 1945

# Viabilização e composição do modelo
ModeloVonNeumann ⇒ ProgramaArmazenado
ModeloVonNeumann ⇒ Computador
ModeloVonNeumann ⊗ {Processador, Memória, MeioExternoDeGravação}

# Estrutura do processador
Processador ⊗ {ULA, CC}
Processador ⊳ Memória

# Estrutura do computador
Computador ⊗ {Hardware, Software}
Computador ⊳ DadosBinários

# Dualidade hardware / software
Hardware ≡ ¬Software                        : contraste conceitual
Processador ⊑ Hardware

# Programa armazenado
ProgramaArmazenado ⊑ Software
ProgramaArmazenado ⊳ DadosBinários          : armazenado como
ProgramaArmazenado ⊳ Processador            : executado por
```

### Ganho da tradução

- **13 arestas** do diagrama original expressas em **13 linhas** de MAPA
- ordem semântica explícita (histórico → composição → dualidade → software)
- cada linha é individualmente editável sem tocar o resto
- parseável por script Dataview sem lidar com sintaxe Mermaid

---

## Exemplos por categoria

### Composição

```mapa
Chipset ⊗ {PontesNorte, PontesSul}
Placa-mãe ⊗ {Chipset, Slots, Barramentos}
```

### Hierarquia de níveis

```mapa
LinguagemAssembly ≺ ISA
ISA ≺ Microarquitetura
Microarquitetura ≺ LógicaDigital
```

### Taxonomia

```mapa
RISC ⊑ ISA
CISC ⊑ ISA
x86 ∈ CISC
ARM ∈ RISC
AVR ∈ RISC
```

### Sucessão histórica

```mapa
Microprogramação → RISC                     : ~1985
ComputadorProgramaFixo → ModeloVonNeumann   : 1945
ProcessadorP6 → ProcessadorCore             : 2006
```

### Combinações compostas

```mapa
(Chipset ⊗ {PontesNorte, PontesSul}) ⇒ Comunicação[Processador, Dispositivos]
Notebook ⊑ PC ∧ ¬DesktopForm
DMA ⊳ Memória ∧ ¬Processador
```

### Equivalência terminológica

```mapa
ULA ≡ CA[VonNeumann]
CentralDeControle ≡ CC
```

---

## Convenções de uso no vault

1. bloco de código com linguagem `mapa`:
   ````
   ```mapa
   Processador ⊗ {ULA, CC}
   ```
   ````
2. colocar o bloco depois da seção "Relações (SPO)" existente, nunca em substituição
3. para termos com espaço ou acento, preferir `CamelCase` ou `[[Wiki-Link]]`
4. uma sentença por linha; nada de concatenação implícita
5. qualificadores de ano em formato livre, curto: `: 1945`, `: ~1985`, `: legado`

---

## Limitações assumidas

- não expressa propriedades numéricas (largura de barramento, clock) — continua no corpo da nota
- não representa condicionais complexos (use texto natural)
- não substitui tabelas detalhadas nem exemplos de código
- não é executável: é linguagem de **anotação**, não de programação

---

## Roadmap (não executar já)

- parser Dataview JS que leia blocos `mapa` e gere Mermaid automaticamente
- export para Turtle/RDF caso seja útil integrar com ferramentas externas
- modo ASCII (`<=`, `==`, `(+)`, `->`, `=>`, `<<`) para quando Unicode for inconveniente
- metadados de aresta (`@ano`, `@fonte`) para rastreabilidade de origem

---

## Ver também

- [[HOW-TO]] — guia prático de uso dos símbolos
- [[Index]] — diagrama original traduzido acima
- [[Computador]], [[Modelo de Von Neumann]], [[ISA]] — notas-piloto com blocos `mapa`
