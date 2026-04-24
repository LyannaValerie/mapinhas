---
title: Desempenho do Processador
tags:
  - hardware
  - arquitetura-x86
  - desempenho
aliases:
  - tecnologias de desempenho
  - otimização de processador
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Desempenho do Processador

Técnicas incorporadas à arquitetura x86 ao longo dos anos para aumentar o desempenho. Ver contexto geral em Capítulo 5 (pipeline CISC básico).

---

## Cache de Memória

Ver [[Cache]] para detalhe completo. Resumo:

- RAM mais lenta que o processador → cache armazena bloco de instruções/dados antecipadamente
- Organização: mapeamento direto / completamente associativo / **associativo por grupos** (mais eficiente)
- Níveis L1 (dividido I+D) → L2 → L3 (compartilhado entre núcleos)
- Política write-back + protocolo **MESI**
- Taxa de acerto: 80–99%

---

## Previsão de Desvios

Cache assume execução sequencial. Desvios condicionais invalidam esse pressuposto.

- **BTB (Branch Target Buffer):** memória interna que guarda histórico de desvios anteriores
- A unidade de previsão analisa o BTB e carrega antecipadamente o endereço mais provável
- Primeira vez: disponível a partir do Pentium (1993)
- Melhora a cada nova microarquitetura (mais unidades em paralelo = previsão mais crítica)

---

## Arquitetura Superescalar

Mais de uma unidade de execução operando em paralelo → mais de uma instrução por ciclo.

- Intel: primeiro Pentium (1993). AMD: K5.
- Unidades divididas em: **inteiros** (x86) e **ponto flutuante** (x87/SIMD)

Ver [[Concorrência e Paralelismo]].

---

## Execução Fora de Ordem

Problema: instruções dependentes entre si travam unidades de execução paralelas.

Soluções:
1. **Compilador reorganiza instruções** — limitado: código otimizado para N unidades ≠ ótimo para M unidades
2. **Execução fora de ordem (hardware):** processador busca instruções não dependentes adiante na fila e as executa nas unidades ociosas

> "Recompilador por hardware" ou "otimizador de sequência de instruções" em tempo real.

---

## Execução Especulativa

Combinação de previsão de desvios + execução fora de ordem:

- A unidade de previsão aposta num desvio condicional → execução especulativa **roda as instruções** desse desvio em unidades ociosas **antes** de o processador chegar ao desvio
- Se acertou: pedaço do programa já processado → ganho de desempenho
- Se errou: resultados descartados

---

## Renomeamento de Registradores

Problema: execução fora de ordem com reuso do mesmo registrador → valores corrompidos.

Solução: **registradores físicos** (internos, opacos a programas) substituem temporariamente os **registradores lógicos/arquiteturais** (os registradores x86/x87 visíveis).

Tabela de mapeamento lógico→físico mantida internamente. Após execução: atualização do estado da máquina (registrador lógico recebe valor do físico).

### Técnicas de implementação

| Técnica | Descrição |
|---------|-----------|
| **RRF** (Real/Rename/Retirement Register File) | Registradores físicos acoplados ao buffer de reordenamento; microinstruções carregam dados junto |
| **PRF** (Physical Register File) | Estrutura independente; microinstruções apontam para posições no PRF; sem movimentação de dados na execução |

---

## Arquitetura Híbrida CISC/RISC

x86 é CISC → não pode ser migrado para RISC (quebraria compatibilidade binária).

Solução: **decodificador interno** converte instruções x86 (CISC) em **microinstruções** (µops, uops) RISC proprietárias.

- Microinstruções são internas e opacas — fabricantes não as divulgam
- 1 instrução CISC simples → 1 µop (rápida)
- 1 instrução CISC complexa → 2+ µops (mais lenta)

---

## Fusão de Microinstruções

Decodificador **funde duas µops em uma** → separadas só na execução.

- Menos µops circulando = menos consumo + mais throughput
- Intel: Banias (Pentium M) em diante. AMD: K7 (Athlon) em diante.

---

## Fusão de Instruções

Converte **duas instruções x86** em **uma única µop**.

- Limitado a: instrução de comparação (`CMP`/`TEST`) + desvio condicional (`Jcc`)
- Economiza uma µop por par fundido

| Disponibilidade | Modo |
|----------------|------|
| Intel Core | apenas 32 bits |
| Intel Nehalem+ | 32 e 64 bits |
| AMD Bulldozer+ | 32 e 64 bits |

---

## Múltiplos Núcleos

Mais de um núcleo de processamento no mesmo chip.

- SO reconhece cada núcleo como processador independente
- Ganho real somente para programas com suporte a **SMP** (multiprocessamento simétrico): edição de vídeo, renderização 3D, cálculo científico, servidores web/BD
- Programas sem suporte a SMP deixam núcleos extras ociosos

> [!note] Equívoco comum
> Múltiplos núcleos não aumentam o número de programas que rodam simultaneamente — multitarefa existe desde o 386 (1985). Núcleos adicionais só ajudam programas que explicitamente enviam threads separadas para cada núcleo.

Primeiros dual-core: Intel Pentium D e AMD Athlon 64 X2 (2005).

Núcleos podem ser independentes ou compartilhar L2/L3. AMD Bulldozer: pares de núcleos compartilham unidade de busca, BTB e L1-I.

---

## Múltiplas Threads (Hyper-Threading / SMT)

SO enxerga **2 núcleos lógicos por núcleo físico**. Internamente: timeslicing entre as duas threads nas unidades internas.

- Aproveita unidades que ficariam ociosas
- Núcleo lógico ≠ núcleo físico em desempenho
- Notação: "4 núcleos / 8 threads" = 4 físicos + HT

| Tecnologia | Fabricante | Lançamento |
|-----------|-----------|------------|
| Hyper-Threading (HT) | Intel | Pentium 4 HT (2003) |
| Simultaneous Multi Threading (SMT) | AMD | Ryzen (2017) |

> [!note] AMD Bulldozer (2011) usou SMT somente na FPU, não nos núcleos inteiros.

---

## Clock Dinâmico

Clock não é fixo — varia automaticamente com carga e limites térmicos/elétricos.

- **Clock base:** valor padrão (conservador)
- **Clock boost:** valor máximo automático (respeitando TDP, temperatura e corrente)
- Ajuste em passos de 100 MHz (Intel) ou 25 MHz (AMD Precision Boost / Zen)
- Reavaliado em frações de segundo; oscila continuamente

> [!tip] Clock boost máximo geralmente só com 1–2 núcleos ativos. Mais núcleos = mais calor = clock menor.

| Tecnologia | Fabricante | Lançamento |
|-----------|-----------|------------|
| Turbo Boost | Intel | Core i (Nehalem, 2008) |
| Turbo Core | AMD | Phenom II série T (K10, 2010) |
| Precision Boost | AMD | Ryzen (Zen, 2017) — passos de 25 MHz |
| XFR (eXtended Frequency Range) | AMD | Zen (2017) — acima do boost nominal |
| Precision Boost Overdrive (PBO) | AMD | Zen+ (2018) — configuração manual de limites |

---

## Ver também

- [[Cache]] — detalhe completo de arquitetura e políticas de escrita
- [[Concorrência e Paralelismo]] — superescalar, ILP, SMP
- [[Multitarefa]] — diferença entre multitarefa e múltiplos núcleos
- [[FPU]] — unidades de ponto flutuante e SIMD
- [[Instruções SIMD]] — paralelismo de dados
- [[Registradores x86]] — registradores lógicos vs. físicos (renomeamento)
- [[Modos de Operação x86]] — fusão de instruções disponível por modo
- [[Otimização de Código]] — como explorar essas técnicas a partir do código C (CPE, loop unrolling, múltiplos acumuladores)
