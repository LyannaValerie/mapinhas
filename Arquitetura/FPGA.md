---
title: FPGA
aliases:
  - Field-Programmable Gate Array
  - LUT
  - Look-Up Table
  - HDL
  - VHDL
  - Verilog
  - PAL
  - PLA
  - ASIC
  - circuito reconfigurável
tags:
  - computação/fundamentos
  - computação/arquitetura
  - computação/hardware
date: 2026-04-12
source: "Organização Estruturada de Computadores — Tanenbaum, Cap. 3"
---

# FPGA

**Field-Programmable Gate Array** — circuito integrado reconfigurável cujo comportamento lógico é definido pelo usuário após a fabricação.

→ Alternativa customizada: ASIC  
→ Antecessores: PAL, PLA  
→ Descrito em: HDL (VHDL, Verilog)  
→ Implementa no nível lógico: [[Portas Lógicas]], [[Circuitos Combinatórios]], [[Latch e Flip-Flop]]

---

## Relações (SPO)

- FPGA → implementa lógica via → LUTs (Look-Up Tables)
- LUT → armazena → tabela-verdade de qualquer função booleana das entradas
- FPGA → reconfigura via → bitstream armazenado em SRAM ou flash
- VHDL/Verilog → descrevem → comportamento do circuito (HDL)
- FPGA → protótipa → hardware antes de ASIC
- ASIC → substitui → FPGA em produção de volume (menor custo/watt, maior desempenho)

---

## Arquitetura Interna

Três componentes principais:

### 1. Blocos Lógicos (CLB / LE)

Cada bloco contém:
- **LUT** (Look-Up Table): memória RAM com $2^n$ posições (n = número de entradas, tipicamente 4–6)
  - Qualquer função booleana de n variáveis pode ser armazenada diretamente na LUT
  - Programar uma LUT = carregar os valores da tabela-verdade
- **Flip-flop D**: registra a saída da LUT (para circuitos sequenciais)
- MUX de saída: seleciona saída combinatória (LUT) ou registrada (FF)

**Exemplo LUT 4 entradas:**  
16 posições de memória. Para implementar `A AND B AND C AND D`: escrever 1 apenas na posição 15 (1111), 0 nas demais.

### 2. Interconexões Programáveis

Matriz de roteamento entre blocos lógicos. Cada cruzamento de fio pode ser conectado ou não via células de programação (SRAM).

Fios segmentados de vários comprimentos + switchboxes → permite qualquer topologia lógica.

### 3. Blocos de I/O

Interfaces com o mundo externo. Programáveis para diferentes padrões elétricos (LVCMOS, LVDS, etc.) e direções (entrada, saída, bidirecional).

---

## Configuração

| Tipo | Memória de configuração | Volatilidade | Notas |
|---|---|---|---|
| **SRAM-based** | SRAM interna | Volátil — recarrega no boot | Mais comum; bitstream em flash externa |
| **Flash-based** | Flash interna | Não-volátil | Funciona imediatamente ao ligar |
| **Antifuse** | Fusível físico | Permanente (não reconfigurável) | Aplicações seguras/militares |

---

## Recursos Adicionais em FPGAs Modernas

- **DSP blocks**: multiplicadores+acumuladores dedicados (muito mais eficientes que LUT)
- **Block RAM (BRAM)**: memória SRAM embutida de alta velocidade
- **PLL / MMCM**: geradores de clock programáveis
- **Transceivers seriais**: PCIe, Ethernet 10G, SATA em velocidades de Gbps
- **Hard IP blocks**: núcleos de CPU ARM embarcados (ex: Xilinx Zynq), PCIe, DDR controllers

---

## HDL — Hardware Description Language

Linguagens para descrever circuitos digitais:

| Linguagem | Origem | Uso |
|---|---|---|
| **VHDL** | Departamento de Defesa EUA (1980s) | Indústria/militar |
| **Verilog** | Empresa privada (1984) | Academia/Silicon Valley |
| **SystemVerilog** | Extensão do Verilog (2002) | Verificação + síntese |

**Fluxo de projeto:**
1. Escrever HDL descrevendo o comportamento
2. Síntese: ferramenta converte HDL em netlist (portas lógicas)
3. Place & Route: mapeia netlist nos LUTs e roteamento do FPGA específico
4. Geração do bitstream
5. Download para a FPGA

---

## FPGA vs ASIC vs PAL

| | FPGA | ASIC | PAL/CPLD |
|---|---|---|---|
| Reconfigurável | Sim (ilimitado) | Não | Limitado |
| Densidade lógica | Média | Alta | Baixa |
| Desempenho | Médio | Máximo | Baixo-médio |
| Custo unitário (volume) | Alto | Baixo | Baixo-médio |
| Custo de design | Baixo | Alto (milhões) | Baixo |
| Tempo para mercado | Dias | Meses | Dias |
| Uso típico | Prototipagem, ASIC small-series, aceleração | Produção em massa (chips, SoCs) | Lógica de cola simples |

**PAL (Programmable Array Logic)** e **PLA (Programmable Logic Array)**: predecessores mais simples com arrays AND/OR programáveis. Substituídos por CPLDs (Complex PLDs) e FPGAs para designs maiores.

---

## Casos de Uso

- **Prototipagem de ASIC**: validar design antes de tapeout (custo de ASIC = milhões de dólares)
- **Aceleração de hardware**: processamento de imagem, criptografia, trading de alta frequência
- **Lógica de cola (glue logic)**: conectar chips com interfaces incompatíveis
- **Emulação**: implementar processadores obsoletos para manutenção de sistemas legados
- **Pequenos volumes**: mercado não justifica ASIC (ex: equipamentos industriais especializados)

---

## Ver também

- [[Portas Lógicas]] — AND/OR/NOT que as LUTs emulam
- [[Latch e Flip-Flop]] — flip-flops D nos blocos lógicos do FPGA
- [[Circuitos Combinatórios]] — funções implementadas nas LUTs
- [[Microprogramação]] — FPGA como alternativa à microprogramação para lógica de controle
- [[Fabricação de Chips]] — ASIC é fabricação dedicada; FPGA é chip de propósito geral
