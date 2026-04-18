---
title: Barramento
aliases:
  - bus
  - omnibus
  - barramento de sistema
tags:
  - computação/arquitetura
date: 2026-04-06
---

# Barramento

## Definição
Conjunto de **fios paralelos** usado para conectar os componentes de um computador — [[Processador]], [[Memória]], dispositivos de E/S — permitindo que dados, endereços e sinais de controle trafeguem entre eles.

## Relações (SPO)
- Barramento → conecta → [[Processador]], [[Memória]], dispositivos de E/S
- Barramento → introduzido comercialmente por → PDP-8 (DEC, ~1965) como "omnibus"
- Barramento → substituiu → arquitetura centrada na memória (modelo IAS)
- Barramento → adotado por → quase todos os computadores de pequeno porte desde o PDP-8

## Tamanho de palavra (word size)

Barramentos transferem dados em **blocos de tamanho fixo** chamados **palavras** (*words*). O tamanho da palavra é um parâmetro fundamental do sistema:

| Arquitetura | Word size | Era |
|---|---|---|
| 32 bits | 4 bytes | PCs dos anos 1990–2000 |
| **64 bits** | **8 bytes** | PCs modernos (x86-64, ARM 64) |

O tamanho da palavra determina: quantidade máxima de memória endereçável, tamanho dos [[Registrador|registradores]] e largura do barramento de dados.

## Origem histórica

> [!info] Ruptura com o modelo IAS
> A máquina IAS de Von Neumann era **centrada na memória** — todos os componentes se comunicavam através dela. O PDP-8 introduziu o **omnibus**: um único barramento ao qual todos os componentes se conectam. Essa arquitetura reduziu drasticamente a complexidade de interconexão e tornou-se o padrão dominante.

## Barramento vs. Conexão Ponto a Ponto

Um **barramento** é um canal **compartilhado** por múltiplos dispositivos. Já uma **conexão ponto a ponto** é um canal **dedicado** exclusivamente a dois dispositivos, oferecendo maior desempenho por eliminar contenção e arbitragem. Ver [[Transmissão de Dados#Topologia do Canal Barramento vs. Ponto a Ponto|Transmissão de Dados § Topologia]].

### Arbitragem
Em um barramento, apenas um dispositivo pode transmitir por vez. Portanto, é necessário:
1. **Sistema de endereçamento** — para identificar o destinatário da mensagem entre todos os dispositivos conectados.
2. **Mecanismo de arbitragem** — para definir quem transmite quando e resolver colisões (tentativas simultâneas de transmissão).

## Conexões no PC: barramento real vs. ponto a ponto

> [!warning] PCI Express e SATA **não** são barramentos
> Embora frequentemente chamado de "barramento PCI Express", o PCI Express é uma **conexão ponto a ponto**. O equívoco vem do seu antecessor, o **PCI** (descontinuado), que era de fato um barramento. Da mesma forma, SATA é ponto a ponto. O USB pode ser ponto a ponto (dispositivo único) ou barramento (via hub).

| Conexão | Tipo real | Função no [[PC]] |
|---|---|---|
| Barramento da memória | **Barramento** | Conecta [[Processador]] ↔ módulos de [[Memória]] (RAM) |
| PCI Express | Ponto a ponto | Conecta [[Placa-mãe]] ↔ placas de expansão |
| SATA | Ponto a ponto | Conecta [[Placa-mãe]] ↔ [[Memória de Massa]] (HDs, SSDs) |
| USB (1 dispositivo) | Ponto a ponto | Conecta [[Placa-mãe]] ↔ periférico único |
| USB (hub / múltiplos) | **Barramento** | Dispositivos compartilham a mesma porta |

## Protocolos de Barramento: Síncrono vs. Assíncrono

### Barramento Síncrono

Todas as transferências governadas por um **sinal de clock** compartilhado.

```
CLK  ___|‾|_|‾|_|‾|_|‾|_
DATA         [endereço][dado]
```

- Protocolo fixo por número de ciclos de clock
- Simples de implementar; alta eficiência com dispositivos de mesma velocidade
- **Limitação:** todos os dispositivos devem operar no mesmo clock; distâncias curtas (clock skew); dispositivos mais lentos forçam ciclos de espera (wait states)

### Barramento Assíncrono (Handshaking)

Sem clock compartilhado. Transferência coordenada por sinais de handshake.

Exemplo com dois sinais: **MSYN** (Master Synchronization) e **SSYN** (Slave Synchronization):

```
1. Master coloca endereço/dado no barramento
2. Master ativa MSYN ("tenho dado")
3. Slave lê dado; ativa SSYN ("recebi")
4. Master desativa MSYN
5. Slave desativa SSYN — transferência concluída
```

- Cada etapa aguarda confirmação antes da próxima (protocolo autossincronizado)
- **Vantagem:** mistura dispositivos rápidos e lentos sem wait states fixos
- **Desvantagem:** mais complexo; overhead de handshake; difícil de implementar em alta velocidade

### Largura e Multiplexagem

**Largura do barramento:** mais linhas paralelas = mais bits por ciclo = maior largura de banda. Custo: mais pinos no chip + mais fios na placa.

**Barramento multiplexado:** o mesmo conjunto de linhas carrega endereços em alguns ciclos e dados em outros. Reduz número de pinos ao custo de mais ciclos por transferência.

---

## Arbitração de Barramento — Detalhes

Num barramento compartilhado, apenas um dispositivo pode transmitir por vez. Quando múltiplos dispositivos querem o barramento simultaneamente, é preciso arbitrar quem ganha.

### Arbitração Centralizada

Um único chip **árbitro de barramento** gerencia todas as requisições.

```
CPU ──REQ──┐
DMA ──REQ──┤  [Árbitro]  ──GNT──→ CPU / DMA / I/O
I/O ──REQ──┘
```

1. Dispositivo ativa linha **REQUEST**
2. Árbitro escolhe (política de prioridade fixa, round-robin ou outra)
3. Árbitro ativa **GRANT** para o vencedor
4. Vencedor usa o barramento; ao terminar, libera (desativa REQUEST)

**Vantagem:** controle centralizado, política flexível.  
**Desvantagem:** ponto único de falha; gargalo no árbitro.

### Arbitração Descentralizada — Daisy-Chain

O sinal GRANT circula em **cadeia** pelos dispositivos. Prioridade determinada pela posição física.

```
[CPU]──GNT──→[DMA]──GNT──→[I/O A]──GNT──→[I/O B]
  ↑
 GRANT entra aqui
```

- Cada dispositivo: se quer o barramento E recebe GRANT → usa o barramento (consome o GRANT)
- Se não quer o barramento → repassa GRANT ao próximo
- Dispositivo mais próximo da fonte tem maior prioridade

**Vantagem:** sem chip árbitro dedicado; simples.  
**Desvantagem:** prioridade fixa por posição; dispositivo com alta prioridade pode monopolizar.

### Arbitração Descentralizada — Detecção de Colisão

Dispositivos monitoram o barramento. Se livre, transmitem. Se colisão detectada (dois transmitiram simultaneamente):
1. Ambos param
2. Aguardam tempo aleatório
3. Retentam

Usado em redes Ethernet (CSMA/CD) e alguns barramentos de baixa velocidade.

**Vantagem:** sem árbitro; descentralizado totalmente.  
**Desvantagem:** não determinista; ineficiente com alta carga.

---

## Arquiteturas de barramento e E/S (Q11)

Dois modelos clássicos de organização de computador com um único barramento vs. múltiplos barramentos:

### Sistema de barramento único
```
[ CPU ] ──── barramento único ──── [ Memória ]
                   │
              [ Dispositivos E/S ]
```
- CPU, memória e todos os dispositivos de E/S **compartilham o mesmo barramento**
- **E/S:** a CPU emite um comando de E/S; o dispositivo transfere dados pelo barramento compartilhado; CPU precisa **aguardar** ou usar interrupções
- **Gargalo:** todas as transferências competem pelo mesmo canal — CPU vs. memória vs. E/S
- **Vantagem:** simplicidade, custo baixo

### Sistema com barramentos separados
```
[ CPU ] ── barramento CPU-memória ── [ Memória ]
   │
   └── barramento de E/S ── [ Controlador E/S ] ── [ Dispositivos ]
```
- Barramento de alta velocidade para CPU ↔ Memória; barramento separado (mais lento) para E/S
- **E/S:** controlador de [[DMA]] transfere dados entre dispositivos e memória **sem envolver a CPU** — via barramento de E/S, em paralelo com o tráfego CPU-memória
- **Vantagem de desempenho:** CPU pode executar instruções enquanto DMA realiza E/S simultaneamente → **melhor desempenho global**
- **Desvantagem:** maior complexidade e custo

> [!info] Qual tem melhor desempenho?
> O sistema de **múltiplos barramentos** tem potencial superior porque elimina a contenção entre tráfego CPU-memória e tráfego de E/S. O barramento único cria um gargalo inevitável — especialmente em sistemas com E/S intensiva.

## Evolução dos Barramentos de E/S em PCs

Histórico dos barramentos de expansão que conectavam dispositivos de E/S ao processador.

| Padrão | Ano | Clock | Largura de dados | Banda máx | Notas |
|---|---|---|---|---|---|
| **ISA** (8 bits) | 1981 | 8 MHz | 8 bits | 8 MB/s | IBM PC original |
| **ISA** (16 bits) | 1984 | 8 MHz | 16 bits | 16 MB/s | IBM AT; dominou até início dos 90s |
| **EISA** | 1988 | 8,33 MHz | 32 bits | 33 MB/s | Compatível com ISA; para servidores |
| **MCA** | 1987 | 10 MHz | 32 bits | 40 MB/s | Proprietário IBM; incompatível com ISA |
| **VL-Bus (VESA)** | 1992 | 33 MHz | 32 bits | 133 MB/s | Extensão do barramento local do 486 |
| **PCI** | 1993 | 33–66 MHz | 32–64 bits | 266–533 MB/s | Independente de processador; padrão até ~2005 |
| **PCI-X** | 1998 | 66–133 MHz | 64 bits | até 1 GB/s | PCI extendido para servidores |
| **AGP** | 1997 | 66 MHz | 32 bits | 266–2133 MB/s | Ponto a ponto dedicado para GPU |
| **PCIe** | 2003 | — | Ponto a ponto em série | ver [[Slots de Expansão]] | Substitui PCI; padrão atual |

> [!warning] PCI ≠ PCIe
> PCI era barramento paralelo compartilhado (múltiplos dispositivos dividem o mesmo canal). PCIe é conexão ponto a ponto serial — arquitetura completamente diferente. "Barramento PCI Express" é equívoco comum. Ver [[Slots de Expansão]].

### Por que o PCI substituiu o ISA?

ISA limitava-se a 8 MHz para manter compatibilidade com periféricos antigos. Com CPUs atingindo 66–100 MHz nos anos 90, o gargalo de E/S tornou-se inaceitável. PCI introduziu:
- **Independência do clock da CPU**: opera a clock próprio
- **Auto-configuração (Plug and Play)**: registradores de configuração padronizados
- **Compatibilidade com múltiplos processadores**: Intel, PowerPC, Alpha

### Por que o PCIe substituiu o PCI?

Barramentos paralelos enfrentam *clock skew* — bits transmitidos em paralelo chegam em momentos ligeiramente diferentes por variações nos fios. Acima de ~100 MHz, o skew torna a sincronização impraticável. Solução: **serial point-to-point** com clock embutido no sinal (8b/10b, 128b/130b).

## Ver também
- [[Slots de Expansão]] — PCIe: pistas, versões, slots, M.2
- [[Placa-mãe]]
- [[Processador]]
- [[Memória]]
- [[DMA]]
- [[História da Computação]]
- [[Rede]] — adaptador de rede conecta-se ao barramento de E/S como qualquer dispositivo
- [[Transmissão de Dados]] — modos simplex/half-duplex/full-duplex aplicam-se às linhas do barramento (PCI Express = simplex duplo; SATA = half-duplex)
- [[Portas Lógicas#Porta Tri-State]] — tri-state permite múltiplos dispositivos compartilhar linhas do barramento
- [[Modem e DSL]] — telecomunicações via linha telefônica e cabo coaxial