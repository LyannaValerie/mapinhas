---
title: Transmissão de Dados
aliases:
  - transmissão de dados
  - data transmission
  - meio de transmissão
  - canal de comunicação
  - modos de transmissão
  - barramento vs ponto a ponto
  - clock de transmissão
  - síncrono
  - assíncrono
  - transmissão paralela
  - transmissão em série
  - transmissão serial
  - transmissão diferencial
  - detecção de erros
  - correção de erros
  - paridade
  - checksum
  - CRC
  - ECC
  - NRZ
tags:
  - computação/fundamentos
date: 2026-04-09
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Transmissão de Dados

## Definição
Processo de mover dados entre componentes internos de um computador ou entre o computador e dispositivos externos. Toda comunicação ocorre por um **meio**, através de um **canal**, segundo um **modo** de transmissão.

---

## Meio

O **meio** (ou mídia) é o **caminho físico** pelo qual os dados circulam. Exemplos:

| Meio | Aplicação típica |
|---|---|
| Fio elétrico | Conexões internas e externas com fio |
| Trilha em PCB | Comunicação entre componentes na [[Placa-mãe]] |
| Ondas de rádio | Transmissões sem fio (Wi-Fi, Bluetooth, rádio) |

## Relações (SPO)
- Meio → é suporte físico de → Canal
- Canal → é criado em → Meio
- Modo → define → direção e simultaneidade da transmissão
- [[Barramento]] → é → canal compartilhado por múltiplos dispositivos
- Conexão ponto a ponto → é → canal dedicado entre dois dispositivos
- Clock → sincroniza → transmissor e receptor
- Transmissão síncrona → usa → clock externo ao canal
- Transmissão assíncrona → usa → clock embutido nos dados
- Transmissão de Dados → ocorre em → [[Barramento]], [[Rede]], interfaces de armazenamento

---

## Canal

Um **canal** é um caminho de comunicação criado no meio para conectar dois dispositivos. A forma de criar canais determina quantas transmissões simultâneas o meio comporta:

### Banda Base
- **Um único canal** é criado no meio.
- Método padrão em **eletrônica digital**.
- Usado na comunicação interna entre componentes de hardware (processador ↔ memória, por exemplo).

### Banda Larga
- **Vários canais** são criados simultaneamente no mesmo meio.
- O receptor precisa **sintonizar** o canal desejado.
- Usado em: rádio, TV aberta, TV a cabo, redes Wi-Fi.

> [!info] Cuidado com o termo "banda larga"
> O termo "banda larga" no contexto de canais (multiplexação) é diferente do uso popular que indica uma conexão de Internet de alta velocidade. Aqui, refere-se especificamente ao método de criar múltiplos canais no mesmo meio físico.

---

## Modos de Transmissão

### Simplex
- O canal é **sempre unidirecional**: os dados fluem em apenas um sentido.
- Para comunicação bidirecional com simplex, são necessários **dois canais separados** — um para transmissão e outro para recepção.

```
A ──────────────────→ B      (simplex único — unidirecional)

A ──────────────────→ B
A ←────────────────── B      (simplex duplo — dois canais separados)
```

> [!example] Exemplo real
> A conexão **PCI Express** usa simplex duplo: dois conjuntos de fios, um para cada direção.

**Vantagem sobre full-duplex:** maior desempenho (dois canais dedicados vs. um canal compartilhado).

---

### Half-Duplex
- O canal é **bidirecional**, mas **não é possível transmitir e receber ao mesmo tempo**.
- Em um dado momento, ou A transmite, ou B transmite — nunca os dois simultaneamente.

```
A ─────────────────────→ B   (A transmite, B recebe)
         OU
A ←───────────────────── B   (B transmite, A recebe)
```

> [!example] Exemplos reais
> - **Walkie-talkie** — dois lados podem falar, mas um de cada vez.
> - **Barramento da memória RAM** — comunicação entre [[Processador]] e [[Memória]].
> - **Portas USB** — comunicação entre [[Placa-mãe]] e periféricos.
> - **Portas SATA** — comunicação entre [[Placa-mãe]] e [[Memória de Massa]].

---

### Full-Duplex
- O canal é **verdadeiramente bidirecional**: A e B podem transmitir e receber **ao mesmo tempo**.

```
A ─────────────────────→ B
A ←───────────────────── B   (simultâneos)
```

> [!example] Exemplos reais
> - **Aparelho telefônico** — ambas as partes falam e ouvem ao mesmo tempo.
> - **Portas SAS** (*Serial Attached SCSI*) — aprimoramento do SATA para servidores; suporta full-duplex.

**Comparação com simplex duplo:** full-duplex usa **um único canal** bidirecional; simplex duplo usa **dois canais** unidirecionais. O simplex duplo tem desempenho superior, mas exige mais fios/trilhas. O full-duplex é mais simples de implementar.

---

## Tabela Comparativa dos Modos

| Modo | Direção | Simultâneo? | Canais necessários | Exemplos no PC |
|---|---|---|---|---|
| **Simplex** | Unidirecional | — | 1 (ou 2 para bidirecional) | PCI Express |
| **Half-duplex** | Bidirecional | Não | 1 | RAM, USB, SATA |
| **Full-duplex** | Bidirecional | Sim | 1 | SAS, telefone |

---

---

## Topologia do Canal: Barramento vs. Ponto a Ponto

A topologia define **quantos dispositivos** compartilham o canal.

### Barramento
- Canal **compartilhado** por múltiplos dispositivos.
- A mensagem pode ser destinada a todos, a alguns, ou a um dispositivo específico.
- Requer **sistema de endereçamento** para que somente o destinatário capture a mensagem.
- Apenas **um dispositivo transmite por vez** — os demais aguardam o canal estar livre.
- Requer **mecanismo de arbitragem**: define quem transmite quando e resolve colisões.

```
A ──┬──── B
    ├──── C       (todos compartilham o mesmo canal)
    └──── D
```

> [!example] Exemplo real
> O **barramento da memória** conecta o [[Processador]] aos módulos de [[Memória|RAM]] — é um barramento real.

---

### Conexão Ponto a Ponto
- Canal **dedicado** e exclusivo entre **dois dispositivos** apenas.
- Nenhum outro dispositivo pode ser conectado ao canal.
- **Maior desempenho** que o barramento: sem contenção, sem arbitragem.

```
A ──────────── B       (canal exclusivo)
```

> [!example] Exemplos reais
> - **SATA** — conexão ponto a ponto entre [[Placa-mãe]] e [[Memória de Massa|HD/SSD]].
> - **PCI Express** — conexão ponto a ponto entre [[Placa-mãe]] e placa de expansão.
> - **USB com um único dispositivo** — ponto a ponto; com hub ou múltiplos dispositivos, torna-se barramento.

> [!warning] Equívoco comum: PCI Express NÃO é um barramento
> PCI Express é frequentemente chamado de "barramento PCI Express" — esta denominação está **incorreta**. PCI Express é uma conexão **ponto a ponto**. O equívoco vem do seu antecessor, o **PCI** (descontinuado), que era de fato um barramento. O nome "Express" foi mantido por tradição de mercado.

### Resumo comparativo

| Característica | Barramento | Ponto a Ponto |
|---|---|---|
| Dispositivos no canal | Múltiplos | Exatamente dois |
| Endereçamento | Necessário | Desnecessário |
| Arbitragem | Necessária | Desnecessária |
| Desempenho | Menor | Maior |
| Exemplos no PC | Barramento RAM | SATA, PCI Express |

---

## Clock

O **clock** ("relógio") é um sinal usado para **sincronizar** o transmissor e o receptor, garantindo que ambos comuniquem no momento certo e na mesma velocidade.

### Por que o clock é necessário?
Se o receptor não estiver preparado para receber (relógio atrasado), ele não capturará os dados ou capturará o dado errado (anterior ou posterior ao pretendido). Da mesma forma, se o transmissor enviar mais rápido do que o receptor consegue capturar, a comunicação falha.

### Medição
O clock é medido em **hertz (Hz)**: um hertz = um ciclo por segundo.

$$T = \frac{1}{f}$$

| Frequência | Período | Dados entregues a cada |
|---|---|---|
| 10 MHz | 100 ns | 100 nanossegundos |
| 1 GHz | 1 ns | 1 nanossegundo |

**Prefixos de tempo úteis:**

| Prefixo | Símbolo | Valor |
|---|---|---|
| mili | m | 10⁻³ |
| micro | μ | 10⁻⁶ |
| nano | n | 10⁻⁹ |
| pico | p | 10⁻¹² |

### Transmissão Síncrona
- Clock fornecido por um **sinal externo** ao canal de dados (geralmente onda quadrada, variando entre 0 e 1).
- Transmissor e receptor usam o mesmo sinal de clock compartilhado.
- A cada pulso de clock, um dado é transmitido.

```
CLOCK:  ┌─┐ ┌─┐ ┌─┐ ┌─┐
        │ │ │ │ │ │ │ │
        ┘ └─┘ └─┘ └─┘ └─

DADOS:  [D1] [D2] [D3] ...
```

### Transmissão Assíncrona
- Clock **embutido junto com os dados** no canal — não há sinal de clock separado.
- O receptor extrai a temporização a partir do próprio sinal de dados.

### Independência dos clocks
Cada sistema de transmissão possui seu **próprio clock independente**:

| Enlace | Clock próprio? |
|---|---|
| [[Processador]] ↔ [[Memória|RAM]] | Sim |
| [[Placa-mãe]] ↔ [[Memória de Massa|HD/SSD]] (SATA) | Sim (independente do anterior) |
| [[Processador]] interno | Sim — sincroniza unidades internas |

> [!info] Clock interno do processador
> O clock interno do [[Processador]] sincroniza a comunicação entre as unidades internas (ULA, registradores, etc.). É independente do clock do barramento de memória. Detalhes no capítulo sobre processadores.

---

## Métodos de Transmissão: Paralela vs. em Série

### Transmissão Paralela
Vários bits são transmitidos **simultaneamente**, cada um em seu próprio fio/trilha.

```
A  ──[D7]──────────────→  B
   ──[D6]──────────────→
   ──[D5]──────────────→
   ──[D4]──────────────→      (8 bits transmitidos ao mesmo tempo)
   ──[D3]──────────────→
   ──[D2]──────────────→
   ──[D1]──────────────→
   ──[D0]──────────────→
   ──[GND]─────────────→
```

**Desvantagem — quantidade de fios:**
- Requer **N + 1 fios** (N bits + terra).
- Exemplo: barramento de memória com 128 bits em paralelo → **129 trilhas** na PCB.

**Desvantagem — ruído eletromagnético (crosstalk):**
- Cada fio gera um campo eletromagnético ao redor.
- Em muitos fios paralelos, o campo de um fio pode corromper os dados do fio adjacente.
- Quanto mais fios, maior a susceptibilidade a interferências.

> [!info] Por que a paralela está em desuso?
> A alta quantidade de fios e a susceptibilidade a ruídos limitaram a frequência de clock que a transmissão paralela pode atingir na prática. Isso levou à migração para a transmissão em série de alta velocidade.

---

### Transmissão em Série

> [!tip] Nomenclatura correta
> O termo correto em português é **"em série"** ou **"serial"** (empréstimo do inglês *serial* — aceitável na área técnica). O que não existe mais é a **porta serial** (RS-232); a **transmissão em série** é hoje a forma dominante de comunicação.

Apenas **um bit é transmitido por vez** no canal.

```
A  ──[1][0][1][0][1][1][1][0]──→  B
         (bits em sequência)
```

**Vantagens:**
- **Poucos fios:** mínimo de 2 (half-duplex) ou 3 (full-duplex).
  - Síncrona: +1 fio para o clock.
  - Bidirecional: frequentemente usa dois canais simplex (par de fios por sentido).
- **Maior imunidade a ruído:** menos fios = menos crosstalk; frequentemente usa [[#Transmissão em Série Síncrona|transmissão diferencial]] para eliminar interferências.
- **Clock mais alto:** com menos fios e menos ruído, é possível operar a frequências muito superiores às da paralela.

**Transmissão em série não é necessariamente mais lenta:**

| Cenário | Resultado |
|---|---|
| Mesmo clock, série vs. paralela de 8 bits | Paralela é 8× mais rápida |
| Série com clock 10× maior que a paralela | Série pode ser mais rápida |
| Comparação correta | Comparar **largura de banda** total, não o método |

> [!example] Exemplos modernos de transmissão em série
> PCI Express, SATA, USB, conexões de rede (Ethernet, Internet) — todos usam transmissão em série.

---

### Transmissão em Série Síncrona

Usa ao menos **um fio adicional** (fora do canal de dados) para o **sinal de clock**. O receptor usa o clock para saber exatamente onde começa e termina cada bit.

```
A  ──CLOCK──────────────────────→  B
   ──DADOS─[0][1][1][0][1][0][1][0]→
```

---

### Transmissão em Série Assíncrona

O mesmo canal de dados também transporta os **sinais de sincronismo** — não há fio de clock separado. É o tipo mais comum de transmissão em série.

**Mecanismo clássico (portas seriais RS-232):**
- **Start bit:** indica o início de um grupo de bits.
- **Stop bit:** indica o fim do grupo.

```
A  ──[START][0][1][1][0][1][0][1][0][STOP]──→  B
```

> [!note] Generalização
> Nem toda transmissão assíncrona usa start/stop bits. Esse é apenas o esquema clássico das antigas portas seriais. Outros protocolos assíncronos usam mecanismos diferentes de sincronismo embutido.

---

### Resumo comparativo dos métodos

| Característica | Paralela | Em Série |
|---|---|---|
| Bits por ciclo de clock | N (largura do canal) | 1 |
| Fios mínimos | N + 1 | 2–3 |
| Susceptibilidade a ruído | Alta (crosstalk) | Baixa |
| Clock máximo prático | Limitado pelo ruído | Muito alto |
| Uso atual | Barramento de memória (interno) | PCI Express, SATA, USB, rede |

---

---

## Transmissão Diferencial

Quase toda transmissão em série moderna usa **transmissão diferencial**. As exceções históricas são a antiga porta serial (RS-232) e conexões de rede por cabo coaxial.

### Como funciona
O mesmo sinal é enviado em **dois fios**:
- **D+** — sinal original com os dados
- **D−** — sinal com a **polaridade invertida**

```
V           D+
            ┌──┐    ┌──┐
─────────── │  │ ── │  │ ──────
            └──┘    └──┘

V           D−
            ┌──┐    ┌──┐
─────────── │  │ ── │  │ ──────   (invertido em relação a D+)
            └──┘    └──┘
```

### Por que funciona
- Cada fio gera um campo eletromagnético ao redor.
- Com D− invertido, o campo gerado é **oposto** ao de D+ → os campos **se anulam** → proteção contra ruído externo.
- O receptor compara D+ e D−: a **diferença** entre os dois contém o dado; qualquer ruído que afete ambos igualmente é cancelado.

### Codificação NRZ Polar
Na transmissão diferencial, usa-se a codificação **NRZ polar**:
- `0` → tensão **negativa**
- `1` → tensão **positiva**

(Ao contrário do NRZ unipolar, onde `0` = 0 V, comum em transmissões paralelas.)

### Par trançado
Em cabos de rede, os pares D+/D− são **trançados** (enrolados entre si), reforçando ainda mais a proteção contra interferências eletromagnéticas externas. Daí o nome **par trançado** (*twisted pair*) dos cabos de rede.

---

## Detecção e Correção de Erros

A transmissão e o armazenamento de dados precisam de mecanismos para verificar se os dados chegaram íntegros.

### Paridade

Adiciona **1 bit extra** a um grupo de dados, de forma que o número total de bits `1` (incluindo o bit de paridade) seja sempre **par**.

**Exemplo — dado 01010001 (três `1`s — ímpar):**
- Transmissor adiciona bit de paridade `1` → `01010001|1` (quatro `1`s — par)
- Receptor recebe `11010001|1` (primeiro bit corrompido) → conta cinco `1`s → ímpar → **erro detectado**

**Limitações:**
- Se um número **par** de bits for invertido, a paridade continua correta → erro **não detectado**.
- Apenas **detecta**; não consegue **corrigir** o erro.

> [!note] RAID e paridade
> Sistemas RAID utilizam um mecanismo de paridade diferente que permite **recuperar** o dado. Ver Capítulo 49.

---

### Repetição (Redundância)

Transmite o mesmo dado **várias vezes**; o receptor decide por **maioria de votos**.

**Exemplo (3 transmissões):**
| Recebido | Interpretação |
|---|---|
| `0 0 0` | Dado íntegro = `0` |
| `1 1 1` | Dado íntegro = `1` |
| `0 0 1` | Maioria = `0` (erro corrigido) |
| `0 1 1` | Maioria = `1` (erro corrigido) |
| `0 1 0` | Maioria = `0` (mas incerto) |

**Limitação:** se o dado for corrompido de forma idêntica todas as vezes, o receptor aceita o valor errado.

---

### Código de Correção de Erros (ECC)

Algoritmo avançado que **detecta e corrige** erros. No PC, usa-se o **código de Hamming**.

**Onde é usado:**
- [[Memória|RAM]] ECC — servidores
- [[Memória de Massa|Memória flash]] (SSDs)
- RAID 2

**Regra de Hamming** — número mínimo de bits de verificação *m* para proteger *k* bits de dados:

$$2^m \geq m + k + 1$$

Os bits de verificação são **intercalados** entre os bits de dados (não colocados no final como na paridade). O algoritmo identifica **qual bit** está errado e o corrige.

---

### Soma de Verificação (Checksum)

A cada *N* dados transmitidos, envia-se um **código de verificação** = soma de todos os dados do grupo.

**Funcionamento:**
1. Transmissor soma os dados → envia dados + checksum.
2. Receptor refaz a soma → compara com o checksum recebido.
3. Se iguais: dados íntegros. Se diferentes: solicita retransmissão.

**Overflow:** se a soma exceder o tamanho do campo de checksum (ex.: resultado de 18 bits em campo de 16 bits), descarta-se os bits mais significativos **ou** soma-se o excedente ao resultado — depende do algoritmo.

**Limitação — colisão:** se a corrupção produzir exatamente o mesmo checksum, o erro passa despercebido. Para checksum de 16 bits, a probabilidade de colisão é **1 em 65.536** (2¹⁶ = 0,0015%).

**Estrutura do pacote de dados:**

```
┌─────────────┬──────────────────┬───────────────┐
│  CABEÇALHO  │      DADOS       │   CHECKSUM    │
│ (controle,  │                  │               │
│  sincronismo│                  │               │
└─────────────┴──────────────────┴───────────────┘
```

---

### CRC — Verificação Cíclica de Redundância

Funciona de modo similar ao checksum, mas em vez de somar, **divide os dados por um polinômio** e usa o resto como código de verificação.

- **Probabilidade de colisão menor** que o checksum → mecanismo mais robusto.
- Apenas **detecta** erros; não corrige.

---

### Resumo comparativo

| Mecanismo | Detecta? | Corrige? | Uso típico |
|---|---|---|---|
| Paridade | Sim (1 bit) | Não | RAM simples, detecção básica |
| Repetição | Sim | Sim (maioria) | Sistemas simples, rádio |
| ECC (Hamming) | Sim | Sim | RAM servidor, SSD, RAID 2 |
| Checksum | Sim | Não | Protocolos de rede, arquivos |
| CRC | Sim (+ robusto) | Não | Ethernet, armazenamento, ZIP |

---

## Codificação

A **codificação** define como bits são convertidos em sinais físicos no canal.

### NRZ — Non-Return to Zero

Cada bit de dados corresponde diretamente a um elemento sinalizador (nível de tensão). Relação **1:1** entre bits e sinais.

| Variante | `0` | `1` | Uso típico |
|---|---|---|---|
| **NRZ Unipolar** | 0 V | tensão positiva | Transmissões paralelas internas, série síncrona simples |
| **NRZ Polar** | tensão negativa | tensão positiva | Transmissão diferencial (quase toda série moderna) |

**Onde NRZ é suficiente:**
- Transmissões **paralelas** internas (dentro de componentes, barramento CPU↔RAM na PCB).
- Transmissões **em série síncronas** (clock externo elimina a necessidade de codificação mais elaborada).

**Limitação do NRZ em série assíncrona de alta velocidade:**
Sequências longas de `0`s ou `1`s sem transições causam dois problemas:
1. **Enfraquecimento diferencial:** a proteção eletromagnética da transmissão diferencial depende de variações no sinal; sequências sem transições a enfraquecem.
2. **Perda de sincronismo:** o receptor pode "se perder" sem transições para re-sincronizar (ficando muito tempo sem um evento 0→1 ou 1→0).

Ambos os problemas aumentam a incidência de erros → mais retransmissões → menor taxa de transferência efetiva.

---

### 8b/10b

Codificação usada em transmissões em série assíncronas de alta velocidade para evitar os problemas do NRZ simples.

**Conexões que usam 8b/10b:**
- PCI Express 1.0 e 2.0
- SATA
- USB 3.0 (USB 3.2 Gen 1×1)
- Gigabit Ethernet (fibra óptica)

> [!note] Outras codificações da família
> 64b/66b, 128b/130b e 128b/132b funcionam de forma análoga — codificam um bloco de dados em um bloco maior — mas com tabelas diferentes e overhead menor.

#### Princípio

Cada grupo de **8 bits de dados** é convertido em **10 elementos sinalizadores** (relação **8:10**). O código é **balanceado**: o número de `0`s é sempre igual ao número de `1`s no grupo de 10 bits. Isso elimina sequências longas do mesmo valor.

O byte de dados é representado como **Dx.y**:
- **x** = valor decimal dos **5 bits menos significativos** (codificados por 5b/6b → 6 bits)
- **y** = valor decimal dos **3 bits mais significativos** (codificados por 3b/4b → 4 bits)

**Exemplo:** `01001011`
- Bits menos significativos: `01011` = 11 → x = 11
- Bits mais significativos: `010` = 2 → y = 2
- Representação: **D11.2**

#### Disparidade (Running Disparity — RD)

Quando um grupo codificado tem mais `1`s do que `0`s (ou vice-versa), a disparidade acumula. Para compensar, existem **duas tabelas de codificação** (−1 e +1); a escolha alterna conforme o balanço acumulado.

**Disparidade** = (número de `1`s) − (número de `0`s). Quando igual a zero, diz-se que o número tem **disparidade zero**.

| Disparidade anterior | Disparidade do número atual | Tabela a usar |
|---|---|---|
| −1 | 0 | −1 |
| −1 | ±2 | +1 |
| +1 | ±2 | −1 |
| +1 | 0 | +1 |

#### Tabela 5b/6b

| Dado | Entrada | Disp. −1 | Disp. +1 |
|---|---|---|---|
| D0 | 00000 | 100111 | 011000 |
| D1 | 00001 | 011101 | 100010 |
| D2 | 00010 | 101101 | 010010 |
| D3 | 00011 | 110001 | 110001 |
| D4 | 00100 | 110101 | 001010 |
| D5 | 00101 | 101001 | 101001 |
| D6 | 00110 | 011001 | 011001 |
| D7 | 00111 | 111000 | 000111 |
| D8 | 01000 | 111001 | 000110 |
| D9 | 01001 | 100101 | 100101 |
| D10 | 01010 | 010101 | 010101 |
| D11 | 01011 | 110100 | 110100 |
| D12 | 01100 | 001101 | 001101 |
| D13 | 01101 | 101100 | 101100 |
| D14 | 01110 | 011100 | 011100 |
| D15 | 01111 | 010111 | 101000 |
| D16 | 10000 | 011011 | 100100 |
| D17 | 10001 | 100011 | 100011 |
| D18 | 10010 | 010011 | 010011 |
| D19 | 10011 | 110010 | 110010 |
| D20 | 10100 | 001011 | 001011 |
| D21 | 10101 | 101010 | 101010 |
| D22 | 10110 | 011010 | 011010 |
| D23 | 10111 | 111010 | 000101 |
| D24 | 11000 | 110011 | 001100 |
| D25 | 11001 | 100110 | 100110 |
| D26 | 11010 | 010110 | 010110 |
| D27 | 11011 | 110110 | 001001 |
| D28 | 11100 | 001110 | 001110 |
| D29 | 11101 | 101110 | 010001 |
| D30 | 11110 | 011110 | 100001 |
| D31 | 11111 | 101011 | 010100 |

#### Tabela 3b/4b

| Dado | Entrada | Disp. −1 | Disp. +1 |
|---|---|---|---|
| Dx.0 | 000 | 1011 | 0100 |
| Dx.1 | 001 | 1001 | 1001 |
| Dx.2 | 010 | 0101 | 0101 |
| Dx.3 | 011 | 1100 | 0011 |
| Dx.4 | 100 | 1101 | 0010 |
| Dx.5 | 101 | 1010 | 1010 |
| Dx.6 | 110 | 0110 | 0110 |
| Dx.7 | 111 | 1110 | 0001 |

#### Símbolos de controle (K)

Com 10 bits de saída para 8 bits de entrada, existem combinações "sobrando". A codificação 8b/10b reserva **12 símbolos especiais de controle** (representados pela letra **K**) para comunicação de controle entre transmissor e receptor. O significado de cada símbolo K depende do protocolo que usa a codificação.

> [!example] Exemplo de codificação — D11.2 (`01001011`)
> 1. `01011` (5 LSBs) = 11 → consulta tabela 5b/6b, D11, disparidade −1 → `110100`
> 2. `010` (3 MSBs) = 2 → consulta tabela 3b/4b, Dx.2, disparidade −1 → `0101`
> 3. Resultado: `110100 0101` (10 bits) — 5 zeros e 5 uns → balanceado → próxima disparidade = −1

#### Tabela de Símbolos de Controle (K)

| Símbolo | Disparidade −1 | Disparidade +1 |
|---|---|---|
| K28.0 | 0011110100 | 1100001011 |
| K28.1 | 0011111001 | 1100000110 |
| K28.2 | 0011110101 | 1100001010 |
| K28.3 | 0011110011 | 1100001100 |
| K28.4 | 0011110010 | 1100001101 |
| K28.5 | 0011111010 | 1100000101 |
| K28.6 | 0011110110 | 1100001001 |
| K28.7 | 0011111000 | 1100000111 |
| K23.7 | 1110101000 | 0001010111 |
| K27.7 | 1101101000 | 0010010111 |
| K29.7 | 1011101000 | 0100010111 |
| K30.7 | 0111101000 | 1000010111 |

---

## Modulação

O **meio físico é analógico** (fios, ondas de rádio). Para transmitir dados digitais, é necessário convertê-los em sinais analógicos — esse processo chama-se **modulação**.

### Modulação por NRZ (caso simples)

Em transmissões por fios ou trilhas na PCB, a modulação geralmente consiste em converter bits em níveis de tensão usando NRZ. Nesse caso, **codificação e modulação se confundem** (são o mesmo processo). Exemplo: na cadeia 8b/10b, o 8b/10b é a *codificação* e o NRZ aplicado depois é a *modulação*.

### Quando é necessária modulação distinta

| Cenário | Por quê NRZ não basta |
|---|---|
| Transmissão **sem fio** | Dados precisam ser convertidos em ondas de rádio |
| Comunicação de **longa distância** por fio | Canal tem características específicas; precisa de formato compatível |

O dispositivo que faz a conversão nos dois sentidos chama-se **modem** (*modulador + demodulador*). Todo acesso à Internet passa por um modem, que converte o padrão de codificação da rede local para o da operadora e vice-versa.

### PAM — Modulação por Amplitude de Pulso

Técnica que usa **mais de dois níveis de tensão**, permitindo codificar mais de um bit por elemento sinalizador.

**Exemplo — PAM4 (4 níveis de tensão):**

| Bits | Tensão |
|---|---|
| `00` | 0,0 V |
| `01` | 1,5 V |
| `10` | 3,0 V |
| `11` | 4,5 V |

Com PAM4, transmitem-se **2 bits por elemento sinalizador** — o dobro do NRZ.

**Exemplo comparativo (canal com 100 MBd de capacidade):**

| Modulação | Bits/sinal | Taxa resultante |
|---|---|---|
| NRZ | 1 bit | 100 Mbit/s |
| PAM4 | 2 bits | 200 Mbit/s |

**Baud (Bd):** unidade de taxa de sinalização = elementos sinalizadores por segundo (norma ISO/IEC 80000-13:2008). Diferente de bit/s.

> [!warning] Desvantagem do PAM
> Mais níveis de tensão → menor "distância" (tolerância) entre cada nível → **maior susceptibilidade a ruído**. Se a tensão variar levemente, o receptor pode interpretar o nível errado. O aumento de erros gera mais retransmissões, reduzindo o throughput real. Por isso, engenheiros analisam cuidadosamente se a modulação mais densa compensa.

> [!note] Fixo no hardware
> O esquema de codificação e modulação de uma conexão (USB, SATA, PCI Express, Ethernet, etc.) é **definido pelos projetistas** e **não é configurável pelo usuário**.

---

## Taxa de Transferência

Mede a **quantidade de dados transferida por unidade de tempo** — a velocidade da transmissão.

### Unidades

| Unidade | Símbolo | Uso típico |
|---|---|---|
| Bytes por segundo | **B/s** | Transmissão paralela; taxa informada pelo SO (downloads, cópias) |
| Bits por segundo | **bit/s** (norma ISO/IEC 80000-13); também bps ou b/s | Transmissão em série (rede, SATA, USB, PCI Express) |
| Baud | **Bd** | Elementos sinalizadores por segundo (canal físico) |

> [!info] Por que o SO sempre usa bytes por segundo?
> O sistema operacional informa taxas de download e transferência de arquivos sempre em B/s — independentemente de a transmissão subjacente ser paralela ou em série.

### Base numérica

- **bit/s** → sempre **base 10**: 2,5 Gbit/s = 2.500.000.000 bit/s.
- **B/s** → pode ser base 10 **ou** base 2 — atenção ao prefixo:
  - `MB/s` (megabyte) = base 10: 533 MB/s = 533.000.000 B/s
  - `MiB/s` (mebibyte) = base 2: 533 MiB/s = 558.891.008 B/s

### Conversão bit/s ↔ B/s

$$\text{B/s} = \frac{\text{bit/s}}{8} \qquad \text{bit/s} = \text{B/s} \times 8$$

> [!example] Equívoco clássico — "100 Mega" de Internet
> Uma conexão de **100 Mbit/s** tem taxa de download de:
> $$\frac{100 \text{ Mbit/s}}{8} = 12{,}5 \text{ MB/s}$$
> O usuário vê "12 MB/s" no SO e acha que a conexão está lenta — mas está perfeitamente normal. A operadora anuncia em **Mbit/s**; o SO exibe em **MB/s**. A confusão vem da diferença de unidade, não de problema na conexão.

> [!note] Relação bit/s, baud e bits por símbolo
> Quando a modulação usa mais de 1 bit por elemento sinalizador (ex.: PAM4 = 2 bits/símbolo):
> $$\text{bit/s} = \text{Baud} \times \text{bits por símbolo}$$
> Ex.: 100 MBd com PAM4 = 100 MBd × 2 = **200 Mbit/s**.

---

---

## Largura de Banda

**Largura de banda** é a **taxa de transferência máxima teórica** de um canal.

### Fórmulas

**Transmissão em série:**
$$\text{Largura de banda} = \text{clock} \times \text{bits por pulso de clock} \tag{4.2}$$

**Transmissão em paralelo** (resultado em B/s):
$$\text{Largura de banda} = \frac{\text{clock} \times \text{bits por pulso de clock}}{8} \tag{4.3}$$

> [!note] Bits por pulso de clock em série
> Não assuma que transmissões em série transferem apenas 1 bit por pulso. Por exemplo, Gigabit Ethernet transfere **2 bits por pulso de clock**. Use sempre o valor correto da especificação.

### Clock efetivo

Fabricantes de memória costumam publicar o **clock efetivo** (clock × bits transferidos por pulso), como se o chip trabalhasse transferindo 1 bit por pulso. Exemplo GDDR6 a "14 Gbps":
- Clock real: 1,75 GHz × 8 bits/pulso = clock efetivo de **14 GHz**
- Para calcular largura de banda, use sempre o clock efetivo.

**Exemplo real — placa de vídeo (256-bit, GDDR6 14 Gbps):**
$$\text{LB} = \frac{14 \text{ Gbit/s} \times 256}{8} = 448 \text{ GB/s}$$

---

### Largura de Banda de Dados vs. Largura de Banda do Canal

A largura de banda do canal é a taxa **bruta** (inclui bits de overhead da codificação). Para obter a taxa de **dados do usuário**, aplica-se:

$$\text{LB de dados} = \frac{\text{bits de dados} \times \text{LB do canal (bit/s)}}{\text{bits transmitidos} \times 8} \tag{4.4}$$

| Codificação | Relação | Eficiência |
|---|---|---|
| NRZ | 1:1 | 100% — LB de dados = LB bruta ÷ 8 |
| 8b/10b | 8:10 | 80% |
| 128b/130b | 128:130 | 98,5% |
| 64b/66b | 64:66 | 97% |
| 128b/132b | 128:132 | 97% |

> [!tip] Simplificação para codificações próximas de 1:1
> Para 64b/66b, 128b/130b e 128b/132b, muitos autores simplificam e dividem a LB bruta por 8 diretamente (erro < 3%). O livro usa a Equação 4.4 para valores precisos.

---

### Tabela 4.5 — SATA (codificação 8b/10b)

| Padrão | Codificação | LB do canal | LB de dados |
|---|---|---|---|
| SATA-150 | 8b/10b | 1,5 Gbit/s | 150 MB/s |
| SATA-300 | 8b/10b | 3 Gbit/s | 300 MB/s |
| SATA-600 | 8b/10b | 6 Gbit/s | 600 MB/s |

*Verificação SATA-600:* (8 × 6 Gbit/s) / (10 × 8) = 600 MB/s ✓

---

### Tabela 4.6 — USB

| Padrão | Nome original | Codificação | LB do canal | LB de dados |
|---|---|---|---|---|
| USB 1.1 Low-Speed | — | NRZ | 1,5 Mbit/s | 187,5 KB/s |
| USB 1.1 Hi-Speed | — | NRZ | 12 Mbit/s | 1,5 MB/s |
| USB 2.0 | — | NRZ | 480 Mbit/s | 60 MB/s |
| USB 3.2 Gen 1×1 | USB 3.0 | 8b/10b | 5 Gbit/s | 500 MB/s |
| USB 3.2 Gen 2×1 | USB 3.1 | 128b/132b | 10 Gbit/s | 1,24 GB/s |
| USB 3.2 Gen 2×2 | — | 128b/132b | 20 Gbit/s | 2,4 GB/s |
| USB4 Gen 2×2 | — | 64b/66b | 20 Gbit/s | 2,4 GB/s |
| USB4 Gen 3×2 | — | 128b/132b | 40 Gbit/s | 4,8 GB/s |

---

### Tabela 4.7 — PCI Express (por lane ×1)

| Padrão | Codificação | LB do canal | LB de dados |
|---|---|---|---|
| PCIe 1.x | 8b/10b | 2,5 Gbit/s | 250 MB/s |
| PCIe 2.0 | 8b/10b | 5 Gbit/s | 500 MB/s |
| PCIe 3.0 | 128b/130b | 8 Gbit/s | 1 GB/s |
| PCIe 4.0 | 128b/130b | 16 Gbit/s | 2 GB/s |
| PCIe 5.0 | 128b/130b | 32 Gbit/s | 4 GB/s |
| PCIe 6.0 | PAM4/FLIT | 64 Gbit/s | 8 GB/s |

> [!example] Série mais rápida que paralela — exemplo concreto
> - Série 1 bit/pulso a 2,5 GHz → **2,5 Gbit/s** = 312,5 MB/s
> - Paralela 32 bits/pulso a 33 MHz → 132 MB/s
>
> A série é mais rápida mesmo transmitindo apenas 1 bit por vez, graças ao clock muito superior.

---

## Fatores que Limitam a Taxa de Transferência

A largura de banda é o **máximo teórico**; a taxa real (**throughput / vazão**) é sempre menor. Principais fatores:

| # | Fator | Explicação |
|---|---|---|
| 1 | **Transmissor não envia continuamente** | Canal fica ocioso quando não há dados a enviar |
| 2 | **Erros → retransmissões** | Bloco inteiro descartado e reenviado; canal ocupado com dados redundantes |
| 3 | **Overhead de protocolo** | Cabeçalho + checksum/CRC ocupam espaço no canal mas não carregam dados do usuário |
| 4 | **Erro de cálculo do usuário** | Não considerar a codificação (ex.: dividir por 8 em canal 8b/10b em vez de por 10) |
| 5 | **Canal compartilhado (barramento)** | Largura de banda dividida entre todos os dispositivos transmitindo simultaneamente |
| 6 | **Limite do dispositivo** | SATA-600 suporta 6 Gbit/s, mas HDDs mecânicos não chegam perto disso |
| 7 | **Eficiência do chip controlador** | Dois dispositivos com a mesma interface podem ter throughput diferente conforme o controlador |

> [!info] Largura de banda ≠ Throughput
> **Largura de banda** = taxa máxima teórica do canal (calculada).
> **Throughput / Vazão** = taxa real observada na prática (sempre ≤ largura de banda).
>
> No caso de Internet e redes, somam-se ainda: infraestrutura da operadora, roteamento, congestionamento e latência — variáveis adicionais além das listadas acima.

---

## Ver também
- [[Barramento]] — canal compartilhado; largura de banda dividida entre transmissões simultâneas
- [[Rede]] — transmissão em série diferencial; par trançado; checksum e CRC nos protocolos; throughput afetado por congestionamento
- [[Placa-mãe]] — superfície física (PCB) onde trilhas formam o meio de transmissão
- [[Memória]] — RAM ECC usa código de Hamming; barramento de memória usa transmissão paralela
- [[Memória de Massa]] — SATA/SAS usam série diferencial; limite do dispositivo frequentemente abaixo da LB do canal
- [[DMA]] — transferências diretas entre dispositivos e memória sem passar pela CPU
- [[Processador]] — clock interno sincroniza unidades internas; clock do barramento é independente
