---
title: HOW-TO — Linguagem Simbólica MAPA
aliases:
  - como usar MAPA
  - guia MAPA
tags:
  - meta/linguagem
  - meta/guia
date: 2026-04-15
---

# HOW-TO — Linguagem Simbólica MAPA

Guia prático. Para a especificação formal, ver [[Linguagem Simbólica]].

---

## Resumo em uma frase

Cada linha de MAPA é: **`sujeito` SÍMBOLO `objeto` `: qualificador opcional`**.

---

## Cola de símbolos

| Símbolo | Lê-se | Exemplo mínimo |
|---|---|---|
| `⊑` | "é tipo de" | `RISC ⊑ ISA` |
| `≡` | "equivale a" | `ULA ≡ CA` |
| `⊗` | "composto por" | `Processador ⊗ {ULA, CC}` |
| `⊳` | "usa / acessa" | `Processador ⊳ Memória` |
| `→` | "foi sucedido por" | `ProgramaFixo → VonNeumann` |
| `⇒` | "viabiliza / implica" | `VonNeumann ⇒ ProgramaArmazenado` |
| `≺` | "nível abaixo de" | `Assembly ≺ ISA` |
| `∈` | "pertence a" | `x86 ∈ CISC` |
| `∧` | "e" | `Notebook ⊑ PC ∧ ¬Desktop` |
| `∨` | "ou" | `RISC ∨ CISC ⊑ ISA` |
| `¬` | "não / contraste" | `Hardware ≡ ¬Software` |
| `{ , }` | conjunto | `{A, B, C}` |
| `[ ]` | parâmetro | `CA[VonNeumann]` |
| `: ...` | qualificador | `→ ... : 1945` |
| `#` | comentário | `# histórico` |

---

## Como escrever um bloco

Use bloco de código com a linguagem marcada como `mapa`:

````
```mapa
Processador ⊗ {ULA, CC}
Processador ⊳ Memória
Processador ⊑ Hardware
```
````

Uma sentença por linha. Sem ponto final. Sem concatenação implícita.

---

## Decisão rápida: qual símbolo usar?

| Pergunta | Símbolo |
|---|---|
| É um tipo/espécie de algo? | `⊑` |
| É composto por partes? | `⊗` |
| Acessa/usa outro componente? | `⊳` |
| Algo veio antes e foi substituído? | `→` |
| Uma ideia viabilizou outra? | `⇒` |
| Um nível está abaixo de outro? | `≺` |
| É um membro de uma família? | `∈` |
| Dois termos nomeiam a mesma coisa? | `≡` |

---

## Receitas prontas

### 1. Anotar composição de componente

```mapa
Placa-mãe ⊗ {Chipset, Slots, Barramentos, VRM}
```

### 2. Anotar especialização com contraste

```mapa
Notebook ⊑ PC ∧ ¬DesktopForm
```

### 3. Anotar cadeia histórica

```mapa
CartãoPerfurado → Batch → Timesharing       : ~1960
```

### 4. Anotar equivalência terminológica antiga/moderna

```mapa
CentralDeAritmética ≡ ULA
MemóriaPrincipal ≡ RAM
```

### 5. Anotar hierarquia de abstração

```mapa
LinguagemAltoNível ≺ Assembly
Assembly ≺ ISA
ISA ≺ Microarquitetura
```

### 6. Anotar taxonomia com múltiplos membros

```mapa
x86 ∈ CISC
ARM ∈ RISC
AVR ∈ RISC
RISC ∨ CISC ⊑ ISA
```

---

## Padrão de integração em nota existente

Estrutura recomendada:

```markdown
## Relações (SPO)
- Computador → processa → [[Dados Binários]]
- Computador → composto por → [[Hardware]] e [[Software]]

## Mapa simbólico
​```mapa
Computador ⊗ {Hardware, Software}
Computador ⊳ DadosBinários
Computador ⊑ ModeloVonNeumann
​```
```

Manter **ambos**: SPO em português (legibilidade) + bloco `mapa` (densidade estrutural).

---

## Erros comuns

| Erro | Correto |
|---|---|
| `Processador ⊗ ULA, CC` | `Processador ⊗ {ULA, CC}` |
| `x86 ⊑ CISC` (quando é instância) | `x86 ∈ CISC` |
| `VonNeumann → EDVAC` (invertido) | `EDVAC ⇒ VonNeumann` |
| Usar `⇒` para composição | Usar `⊗` |
| Frase em linguagem natural no bloco | Usar só símbolos definidos |
| Múltiplas sentenças na mesma linha | Uma por linha |

---

## Quando NÃO usar MAPA

- descrever valores numéricos (clock, tamanho de cache) — texto ou tabela
- descrever comportamento dinâmico (pipeline, interrupção) — texto
- substituir definição em prosa — MAPA complementa, não substitui
- exemplos de código — usar blocos de linguagem real

---

## Convenções de grafia

1. **sem espaço em identificador**: `ModeloVonNeumann`, não `Modelo Von Neumann`
2. **wiki-link quando houver nota**: `[[ULA]] ⊗ ...`
3. **acentos permitidos**: `Memória`, `Computação`
4. **qualificador curto**: `: 1945`, `: legado`, `: x86 apenas`

---

## Checklist antes de commitar um bloco `mapa`

- [ ] cada linha é uma única sentença binária
- [ ] cada símbolo usado existe na [[Linguagem Simbólica#Núcleo de operadores|tabela de núcleo]]
- [ ] termos compostos usam `{}` ou `[]`
- [ ] nenhuma linha mistura prosa com símbolos
- [ ] comentários `#` só para contexto, não para lógica
- [ ] qualificador `:` é curto (≤ 5 palavras)

---

## Ver também

- [[Linguagem Simbólica]] — especificação completa
- [[Computador]], [[Modelo de Von Neumann]], [[ISA]] — notas-piloto com blocos reais
- [[Index]] — grafo original em Mermaid (pode ser lido junto à tradução MAPA)
