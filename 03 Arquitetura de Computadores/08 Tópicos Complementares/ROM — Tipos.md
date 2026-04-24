---
title: ROM — Tipos
aliases:
  - Mask-ROM
  - PROM
  - EPROM
  - EEPROM
  - Flash-ROM
  - memória não-volátil
tags:
  - computação/hardware
  - computação/arquitetura
date: 2026-04-12
source: "Livro Hardware clubedohardware.com.br — Cap. 34"
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# ROM — Tipos

**ROM** (Read-Only Memory): acesso aleatório, não-volátil, escrita lenta ou impossível.

> [!note] ROM também é de acesso aleatório
> O nome "RAM = acesso aleatório" é injusto: ROM também pode ser acessada aleatoriamente. A distinção real é escrita (RAM) vs. somente leitura (ROM).

→ Conceito de volatilidade e uso: [[Memória]]  
→ Firmware gravado na ROM: [[Firmware]]  
→ Chip SPD do módulo de memória (EEPROM): [[RAM — Tecnologias e Módulos]]

---

## Relações (SPO)

- ROM → armazena → [[Firmware]] (BIOS/UEFI, POST, BIOS setup)
- ROM → preserva → conteúdo sem alimentação elétrica
- Flash-ROM → usa → memória flash tipo NOR
- EEPROM → apaga via → impulso elétrico (por endereço)
- Flash-ROM → apaga via → impulso elétrico (conteúdo inteiro)
- Firmware → recebe nome → firmware (de "firme" = fixo, não alterável)

---

## Hierarquia de Tipos

| Tipo | Gravação | Apagamento | Local de gravação | Endereço individual |
|---|---|---|---|---|
| **Mask-ROM** | Fábrica do chip | Impossível | Fabricante do chip | — |
| **PROM** | Fabricante do equipamento | Impossível | Fabricante do equipamento | — |
| **EPROM** | Fabricante do equipamento | UV (minutos; fora do circuito) | Gravador externo | Não |
| **EEPROM** | Fabricante do equipamento | Elétrico (no circuito, se suportado) | Gravador ou in-circuit | **Sim** |
| **Flash-ROM** | Fabricante do equipamento | Elétrico rápido (conteúdo inteiro) | Software no próprio sistema | Não |

### Mask-ROM
Conteúdo gravado na fabricação do chip. Fabricada sob encomenda. Não regravável.

### PROM (Programmable ROM)
Vendida virgem; gravada pelo fabricante do equipamento. Não regravável após a gravação.  
Diferença da Mask-ROM: **local** de gravação (não a fábrica do chip).

### EPROM (Erasable Programmable ROM)
Apagamento por **luz ultravioleta** (janela transparente no encapsulamento).  
Processo: remover do circuito → expor à UV (minutos) → regravar em gravador externo.

### EEPROM (Electrically Erasable Programmable ROM / E²PROM)
Apagamento por impulso elétrico, **sem remoção do circuito** (se o fabricante suportar).  
Permite apagar/regravar **um único endereço** → usado em chips de configuração (SPD dos módulos de memória acessado via I2C/SMBus).

### Flash-ROM
EEPROM com **tensões de apagamento baixas** e apagamento muito mais rápido.  
Tipo **NOR** (usado em ROM de placa-mãe) — ver [[Memória Flash]] (Cap. 35).  
- Não permite apagar endereço individual: apaga e regrava **conteúdo completo**
- Permite atualização via software no próprio sistema ("upgrade de BIOS")
- Tipo mais usado atualmente como ROM em circuitos eletrônicos

---

## Firmware e ROM

**Firmware** = programa gravado em ROM. Nome vem de "firm" (firme) — programa fixo.  
Como "hardware" e "software", é invariável em inglês ("firmwares" não existe).

### Onde ROM está presente

- Placa-mãe (ROM principal: UEFI/BIOS + POST + BIOS setup)
- Placas de vídeo avulsas
- SSDs e discos rígidos
- Unidades ópticas
- Qualquer circuito com processador: injeção eletrônica, micro-ondas, impressoras, smartphones, consoles

> Processadores de periféricos (SSD, HDD, óptica) são específicos e não programáveis pelo usuário.  
> GPU (placa de vídeo) e CPU são programáveis pelo usuário.

### ROM em cartuchos (histórico)
Consoles antigos e computadores com cartuchos: jogo inteiro gravado em Mask-ROM ou PROM no cartucho.

---

## Conexões com Outras Notas

- [[Firmware]] — BIOS/UEFI/POST; detalhes de limitações e CMOS RAM
- [[Memória]] — RAM (volátil) vs ROM (não-volátil); tabela comparativa
- [[RAM — Tecnologias e Módulos]] — chip SPD do módulo = EEPROM acessada via I2C
- [[Hierarquia de Memória]] — ROM fora da pirâmide principal (não é hierarquia de cache)
