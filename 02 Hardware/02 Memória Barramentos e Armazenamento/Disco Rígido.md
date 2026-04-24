---
title: Disco Rígido
aliases:
  - HD
  - HDD
  - Hard Drive
  - Hard Disk Drive
  - Winchester
  - SSHD
  - disco híbrido
  - prato
  - trilha
  - setor
  - cilindro
  - CHS
  - Advanced Format
  - AF
  - S.M.A.R.T.
  - formatação de baixo nível
  - formatação de alto nível
  - bad blocks
  - buffer de disco
  - voice coil
  - servo
  - gravação perpendicular
  - desfragmentação
tags:
  - computação/fundamentos
  - computação/armazenamento
  - computação/hardware
date: 2026-04-12
source: "Livro Hardware clubedohardware.com.br — Cap. 47"
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Hardware]]

# Disco Rígido

Periférico de [[Memória de Massa]] magnético. Armazena SO, programas e dados de forma persistente. Conectado ao computador via [[Portas de Comunicação]] (SATA, SAS, USB).

> [!note] Terminologia
> - "Disco rígido mecânico" é pleonasmo — todo HD é mecânico
> - "Winchester" = codinome IBM 1973 de uma tecnologia específica; não é sinônimo de HD
> - Porta SATA/SAS não é "controladora" — controle está no próprio disco

> [!important] Processamento não ocorre no disco
> Dados no HD precisam ser transferidos para [[Memória]] (RAM) antes de serem processados pelo processador.

---

## Geometria

Sistema de endereçamento físico: **CHS** (Cylinder, Head, Sector).

| Termo | Definição |
|---|---|
| **Prato** | Disco magnético interno. HD tem 1 ou mais. |
| **Face** | Cada lado de um prato. |
| **Cabeça** | Uma cabeça de leitura/gravação por face. |
| **Trilha** | Anel concêntrico em uma face. |
| **Cilindro** | Conjunto de trilhas de mesmo número em todas as faces. |
| **Setor** | Subdivisão de uma trilha. Unidade mínima de leitura/gravação. |
| **Zona** | Conjunto de trilhas com o mesmo número de setores. |

Trilhas externas têm mais setores que trilhas internas. Número de setores por trilha informado pelo fabricante = **média**.

Capacidade total = total de setores × tamanho do setor.

### Tamanho dos Setores

| Formato | Tamanho | Observação |
|---|---|---|
| Tradicional | 512 bytes | Padrão histórico |
| **AF (Advanced Format)** | 4 KiB (4.096 bytes) | Padrão atual; menos overhead de cabeçalhos/rodapés |

Cada setor contém: **cabeçalho** (endereçamento) + **dados** + **rodapé** (checksum). AF reduz proporção de overhead → mais espaço para dados.

---

## Capacidade de Armazenamento

**Fabricantes**: base 10 (MB, GB, TB).  
**Sistema operacional**: base 2 (MiB, GiB, TiB).  
**Windows**: reporta GiB/TiB com rótulo "GB"/"TB" → confusão de usuários.

Exemplo: HD de 1 TB = 1.000.204.886.016 bytes = **931,51 GiB** (reportado pelo Windows como "931,51 GB").

> [!note] Não há "perda" na formatação
> Windows reporta capacidade em base 2 com nomenclatura de base 10. Não há perda de espaço — apenas diferença de unidade.

Mesmo vale para SSDs, pen drives e cartões de memória: capacidade rotulada em base 10.

---

## Limites de Capacidade

| Limite | Causa | Solução |
|---|---|---|
| **2 TiB** | **MBR** usa endereçamento de 32 bits | Usar **GPT** (GUID Partition Table) |

**GPT**: endereçamento 64 bits → suporta até 8 ZiB teoricamente; Windows suporta até 18 EiB; NTFS suporta até 256 TiB.  
Requisito: firmware **UEFI** (ver [[Placa-mãe]]). Disponível a partir do Windows XP 64 bits.

---

## Formatação

### Baixo Nível (Física)
Divide a superfície em trilhas e setores; grava servos. Feita na **fábrica**. Não pode ser repetida pelo usuário — apagaria os servos, destruindo o disco permanentemente.

> [!warning] "Formatadores de baixo nível" modernos
> Não fazem formatação de baixo nível real. Fazem varredura de bad blocks e atualizam a tabela de setores defeituosos. Setores ruins → marcados na tabela → setor reserva assume; capacidade mantida (1 setor reserva por trilha).

### Alto Nível (Lógica)
Cria estruturas usadas pelo SO (diretório raiz, tabelas de localização de arquivos). **Não apaga dados** — apenas apaga metadados de localização.

- Formatação "completa" do Windows = verifica bad blocks; não apaga setores
- Arquivos "apagados" = marcados como apagados no diretório; dados permanecem até serem sobrescritos
- Recuperação possível via software enquanto setores não forem reutilizados

> [!tip] Descarte seguro
> Para destruir dados permanentemente: zerar todos os setores (utilitários do fabricante) ou destruir fisicamente os pratos (furadeira).

---

## Arquitetura Mecânica

- Disco selado; abre apenas em **sala limpa** (mais limpa que sala de cirurgia)
- Cabeças **não tocam** a superfície — flutuam sobre colchão de ar
- Toque = risco de riscar a superfície magnética → perda de dados
- Ao desligar: braço move-se para posição de **estacionamento** e trava → seguro para transporte
- Preocupação real: impactos **com disco girando**

### Mecanismos

| Componente | Função |
|---|---|
| **Servo** | Sinal gravado entre trilhas; orienta o braço ao cilindro correto |
| **Voice coil** | Mecanismo de movimentação do braço; guiado por servos; muito mais rápido que motor de passo |
| **Buffer** | Chip de memória no disco; armazena cilindro inteiro lido; evita retorno à parte mecânica |

Ao ler um setor: braço vai ao cilindro e lê **todos os setores do cilindro** → grava no buffer. Próximas leituras no mesmo cilindro = servidas do buffer.

**Desfragmentação**: arquivos contíguos → cabeças leem vários setores do mesmo arquivo "de uma vez" → menos movimentos do braço → melhor desempenho.

### Velocidade de Rotação e Acesso

| Velocidade | Período por trilha | Uso típico |
|---|---|---|
| 5.400 rpm | ~11,1 ms | Portátil, green |
| 7.200 rpm | ~8,33 ms | Desktop |
| 10.000 rpm | ~6 ms | Servidor alto desempenho |
| 15.000 rpm | ~4 ms | Servidor alto desempenho |

---

## Disco Rígido Híbrido (SSHD)

HD com memória flash integrada à placa lógica. Flash armazena dados mais acessados → leitura rápida (eletrônica). Flash **não acessível separadamente** — uso exclusivo do cache interno.

SSHD = Solid-State Hybrid Drive.

---

## S.M.A.R.T.

Self-Monitoring, Analysis and Reporting Technology. Monitora parâmetros para prever falhas:

- Taxa de erros de leitura acima do normal
- Taxa de transferência abaixo do normal
- Tempo para atingir velocidade de rotação acima do normal
- Número de setores reservas usados alto (muitos bad blocks)
- Cabeças muito próximas da superfície

Habilitado no BIOS Setup (automático em placas modernas). Permite backup preventivo antes da falha.

---

## Interfaces de Comunicação

| Interface | Status | Observação |
|---|---|---|
| ST-506 / ST-412 | Obsoleta | Primeira para PCs; controle em placa ISA separada |
| ESDI | Obsoleta | Parte do controle no disco; pouco popular no Brasil |
| SCSI | Obsoleta | Controle no disco; Mac e servidores; substituída por SAS |
| **PATA (IDE)** | Obsoleta | Mais popular antes do SATA |
| **SATA** | Atual | Desktop e portátil. Ver [[Portas de Comunicação#Portas SATA]] |
| **SAS** | Atual (servidores) | Sucede SCSI. Ver [[Portas de Comunicação#Portas SAS]] |
| USB | Externo | Unidades externas. Mínimo USB 3.0 recomendado. Ver [[Portas de Comunicação#USB]] |
| Thunderbolt | Externo | Menos comum. Ver [[Portas de Comunicação#Thunderbolt]] |

> [!warning]
> Porta SATA em HD SAS: possível. Porta SAS em HD SATA: **impossível**.  
> Largura de banda da porta ≠ taxa de transferência real do disco.

---

## Mercados

| Segmento | Rotação | Tamanho | Diferencial |
|---|---|---|---|
| **Green** | 5.400 rpm | 3,5" | Baixo consumo; menor desempenho |
| **Desktop** | 7.200 rpm | 3,5" | Equilíbrio geral |
| **Portátil** | 5.400 rpm | 2,5" ou 1,8" | Menor consumo; menor tamanho |
| **Enterprise** | 7.200–15.000 rpm | 3,5" | 24/7; alta confiabilidade; SAS; contratos de garantia rígidos |
| **Vigilância** | 5.400–7.200 rpm | 3,5" | Gravação contínua; 24/7 |

HD 2,5" pode ser instalado em desktop sem problemas (via adaptador de bay).

---

## Características Técnicas

| Parâmetro | Descrição |
|---|---|
| **Capacidade** | Base 10 (GB/TB); SO reporta em base 2 (GiB/TiB) |
| **Velocidade de rotação** | rpm; maior = mais rápido e maior consumo |
| **Tamanho físico** | 3,5" (desktop/servidor) ou 2,5" (portátil) |
| **Interface** | SATA (desktop/portátil), SAS (servidor), USB/Thunderbolt (externo) |
| **Buffer** | Maior buffer → mais provável que próximos dados já estejam em cache |
| **Taxa de transferência** | "Sustained data rate" (fabricante); valor real só por benchmark |
| **Tempo de acesso (latência)** | ms; menor = melhor |
| **Consumo** | Watts; relevante em portáteis |
| **Densidade magnética** | bits/polegada²; maior densidade → maior capacidade e desempenho |

### Gravação Perpendicular
Setores gravados perpendicularmente à superfície ("em pé") em vez de paralelos ("deitados"). Mais setores por trilha → maior densidade → maior capacidade e desempenho.

---

## Relações (SPO)

- HD → armazena → dados de forma magnética e persistente
- HD → requer → transferência para [[Memória]] (RAM) antes do processamento
- Setor → é → unidade mínima de leitura/gravação
- Cilindro → agrupa → trilhas de mesmo número em todas as faces
- Voice coil → posiciona → cabeças via leitura de servos
- Buffer → armazena → cilindro lido; serve próximas leituras sem acionar parte mecânica
- Desfragmentação → aumenta → desempenho ao tornar setores contíguos
- S.M.A.R.T. → monitora → parâmetros para previsão de falha
- SSHD → combina → HD mecânico + flash de cache não acessível separadamente
- MBR → limita → partição a 2 TiB; GPT remove esse limite com UEFI
- Formatação alto nível → não apaga → dados dos setores; apenas metadados de localização

---

## Ver também

- [[Memória de Massa]] — conceito geral de armazenamento persistente
- [[Memória]] — RAM; destino dos dados antes do processamento
- [[Memória Flash]] — NAND: base tecnológica dos SSDs
- [[SSD]] — alternativa eletrônica ao HD; NVMe, wear leveling, TRIM
- [[Portas de Comunicação]] — SATA, SAS, USB, Thunderbolt (interfaces de conexão)
- [[Placa-mãe]] — UEFI (necessário para GPT além de 2 TiB)
- [[Unidades Métricas]] — GB vs GiB; base 10 vs base 2
