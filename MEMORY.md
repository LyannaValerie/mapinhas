# MEMORY — Knowledge Vault Session Log

---

## Ingestão 82 — 2026-04-13

### Texto Processado
- **CS:APP Cap. 5** — Optimizing Program Performance: CPE, optimization blockers, code motion, ILP techniques (loop unrolling, múltiplos acumuladores, reassociação), limites (latency/throughput bound), caminho crítico, register spilling, write/read dependency, profiling

### Notas criadas
| Nota | Conteúdo |
|---|---|
| [[Otimização de Código]] | CPE; aliasing de memória e side effects como blockers; code motion; acumular em variável local; grafo de fluxo de dados e caminho crítico; latency/throughput bound (tabela Haswell); loop unrolling k×1; múltiplos acumuladores k×k; reassociação k×1a; register spilling; conditional moves; write/read dependency; profiling com gprof |

### Notas atualizadas
| Nota | O que foi adicionado |
|---|---|
| [[Desempenho do Processador]] | Link para [[Otimização de Código]] na seção Ver também |

### Conexões criadas
- Otimização de Código → Execução Fora de Ordem, Pipeline, Previsão de Desvio, Cache, Lei de Amdahl, Instruções SIMD
- Desempenho do Processador → Otimização de Código (bridge: hardware vs. perspectiva do programador)

### Lacunas pendentes
- Análise de prefixo sum (psum) com reassociação: CPE < latência de FP add — não detalhada (exercício 5.19)
- Profiling do exemplo n-gram Shakespeare: não criado como nota separada (exemplo ilustrativo apenas)

---

## Ingestão 81 — 2026-04-13

### Texto Processado
- **CS:APP Cap. 4** — Processor Architecture: Y86-64 ISA, lógica HCL, SEQ, pipelining (PIPE), hazards, forwarding, CPI

### Notas criadas
| Nota       | Conteúdo                                                                                                                                                             |
| ---------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [[Y86-64]] | ISA pedagógica: 15 registradores, instrução encoding (icode\|ifun), OPq/jXX/cmovXX, status codes AOK/HLT/ADR/INS, tabela de register IDs, convenções pushq/popq %rsp |

### Notas atualizadas
| Nota | O que foi adicionado |
|---|---|
| [[Pipeline]] | Modelo SEQ 6 estágios; forwarding (5 fontes, 2 destinos, prioridade); load/use hazard (condição + solução); controle stall/bubble por condição; fórmula CPI = 1,0 + lp + mp + rp com valores de referência (CPI≈1,27) |
| [[Previsão de Desvio]] | Estratégias estáticas nomeadas: AT (~60%), NT (~40%), BTFNT (~65%); explicação da heurística backward/forward |

### Conexões criadas
- Y86-64 → Pipeline (implementações SEQ/PIPE)
- Pipeline.CPI → Previsão de Desvio (mp=0,16 é maior penalidade)

### Lacunas pendentes
- HCL (Hardware Control Language): linguagem de descrição de controle lógico — não criada (escopo muito específico de CS:APP)
- Implementação Verilog do PIPE: mencionada no cap. mas não coberta

---

## Ingestões 79–80 — 2026-04-12

### Textos Processados
- **Cap. 43:** Aceleração de Mídia (decodificadores/codificadores UVD+VCE/PureVideo+NVENC/QuickSync; recursos: desentrelacamento, telecinagem reversa 2:3, escalonamento, deblocking, denoising, ruído mosquito, edge enhancement, correção de cor)
- **Cap. 44:** Aceleração 3D (eras jogos 2D/2.5D/3D; pipeline rasterização 8 etapas; z-buffer/ROP/TMU; ray tracing raios visualização/sombra/refração/reflexo; BVH; sombreadores ray tracing; técnicas AA SSAA/MSAA/CSAA/EQAA/MFAA/MLAA/FXAA/SMAA/TAA/TXAA; filtragem anisotrópica; V-Sync/screen tearing; DLSS/FSR/RSR)

### Notas criadas
| Nota | Conteúdo |
|---|---|
| [[Aceleração de Mídia]] | Tabela fabricante/decodificador/codificador; desentrelacamento; telecinagem reversa padrão 2:3 (24→30fps TV); escalonamento; deblocking; denoising; ruído mosquito; edge enhancement; correção de cor |
| [[Aceleração 3D]] | Eras 2D/2.5D/3D (Quake 1996, GeForce256 1999); fps (mín 30, ideal 60); rasterização pipeline (8 etapas: instruções→vértices→tesselação→geometria→primitivas→rasterização→pixels→ROP); z-buffer; clipping+face culling; DX10 unidades unificadas; ray tracing (visualização/sombra/refração/reflexo + 5 GRay/s mínimo); BVH; pipeline ray tracing + 5 sombreadores; híbrido rasterização+RT; tabela AA (SSAA/MSAA/CSAA/EQAA/MFAA/MLAA/FXAA/SMAA/TAA/TXAA); filtragem anisotrópica (MIP isoótropico vs AF); V-Sync/screen tearing; DLSS/FSR/RSR (subescalonamento vs SSAA) |

### Notas atualizadas
| Nota | O que foi adicionado |
|---|---|
| [[Placa de Vídeo]] | Links para [[Aceleração de Mídia]] e [[Aceleração 3D]] em Ver também |

### Conexões novas
- **Telecinagem reversa ↔ [[Princípios de Vídeo]] varredura entrelaçada:** processo 2:3 é consequência direta da varredura entrelaçada na TV (60 campos/s) aplicada a filmes (24fps)
- **ROP taxa de preenchimento ↔ VRAM barramento:** ROP grava na VRAM; largura de banda do barramento de memória de vídeo (cap. 42) limita a fill rate da ROP
- **DLSS vs FSR/RSR disponibilidade ↔ drivers:** FSR/RSR funcionam em GPUs NVIDIA sem DLSS — dá contexto para comparações AMD vs NVIDIA
- **GPU CPU carga ↔ arquitetura:** cálculos físicos é CPU; cálculos geométricos e de renderização são GPU — esclarece por que CPU não é gargalo em jogos com GPU potente

---

## Ingestão 78 — 2026-04-12

### Texto Processado
- **Fonte:** Livro de Hardware (clubedohardware.com.br) — Capítulo 42: Placa de Vídeo (arquitetura; GPU/VRAM/RAMDAC/LVDS/VRM/PCIe; cenários discreta/integrada/soldada; UMA; frame buffer; double buffering; mitos VRAM; conectores auxiliares PEG/12VHPWR; modos texto/gráfico; aceleratracao 2D/mídia/3D; APIs DirectX/OpenGL/Vulkan; GPGPU/CUDA; NVLink; multi-monitor)

### Notas criadas
| Nota | Conteúdo |
|---|---|
| [[Placa de Vídeo]] | GPU (chip) ≠ placa de vídeo (placa); 3 cenários (discreta/integrada/soldada); diagrama arquitetura PCIe→GPU→VRAM/RAMDAC/LVDS; UMA (RAM principal como VRAM); frame buffer = pixels×bits÷8; double/triple buffering; mitos VRAM (quantidade/geração/bits barramento); conectores PEG 6/8/12/12+4 pinos + tabela potência total; 12VHPWR ATX 3.0; AWG; modos texto/gráfico; aceleracão 2D/mídia/3D; APIs tabela (DirectX vinculado ao Windows); GPGPU/CUDA/OpenCL; LHR/CMP; CrossFire/SLI descontinuados; NVLink versões; múltiplos monitores |

### Conexões novas
- **GPU clock dinâmico ↔ [[Processador]]:** GPU varia clock conforme carga — mesmo mecanismo do clock dinâmico da CPU (DVS/DVFS)
- **Frame buffer → memória de vídeo ↔ [[RAM — Tecnologias e Módulos]]:** GDDR6 e HBM são tecnologias de VRAM já documentadas; UMA usa DDR da RAM principal como VRAM
- **VRM da placa de vídeo ↔ [[Circuito Regulador de Tensão]]:** mesmo princípio (fases, PWM, DrMOS); formato a+b (GPU + memória) analógico ao a+b da placa-mãe
- **PCIe x16 75W + PEG ↔ [[Slots de Expansão]]:** slot x16 limita a 75W; conectores PEG adicionais são a extensão de alimentação para GPUs de alto TDP

---

## Ingestão 77 — 2026-04-12

### Texto Processado
- **Fonte:** Livro de Hardware (clubedohardware.com.br) — Capítulo 41: Princípios de Vídeo (pixel/RGB; varredura progressiva e entrelaçada; resolução + nomes; relação de aspecto; profundidade de cores + RGBA/CMYK; espaço de cores + gama; taxa de atualização; subamostragem de cores J:a:b)

### Notas criadas
| Nota | Conteúdo |
|---|---|
| [[Princípios de Vídeo]] | Pixel (3 subpixels RGB); varredura progressiva vs entrelaçada (campos par/ímpar, TV 30fps→60fps); resolução tabela VGA→4K/8K; relação de aspecto 4:3/16:9/16:10; profundidade 8/24/30/32 bits (RGBA alfa=transparência, CMYK impressão); espaço de cores sRGB/DCI-P3/Adobe RGB/REC.2020; gama de cores %; taxa de atualização 60 Hz mínimo/cintilação; fórmula largura de banda (col×lin×bits×Hz); subamostragem 4:4:4/4:2:2/4:2:0 (luma/croma, -1/3 e -1/2 largura de banda) |

### Conexões novas
- **Largura de banda de vídeo ↔ [[Portas de Comunicação]]:** fórmula col×lin×bits×Hz determina largura de banda mínima da conexão; subamostragem é mecanismo para encaixar no canal disponível
- **Profundidade de cores RGBA ↔ memória de vídeo:** canal alfa usado internamente pela GPU — implica que framebuffer precisa de 32 bits/pixel mesmo exibindo 24 bits
- **Taxa de atualização 60 Hz ↔ rede elétrica:** mínimo fíunção da frequência da rede (60 Hz Brasil, 50 Hz Europa) — conexão com [[Placa-mãe]] que recebe alimentação AC dessa frequência

---

## Ingestão 76 — 2026-04-12

### Texto Processado
- **Fonte:** Livro de Hardware (clubedohardware.com.br) — Capítulo 40: Circuito Regulador de Tensão (VRM: VID/VCORE/VCC; arquitetura controlador PWM + fases; dobradores de fase; formato a+b+c; componentes: controlador analógico/digital, MOSFETs/DrMOS/RDS(on), indutores ferro/ferrite, capacitores eletrolítico/sólido/tântalo)

### Notas criadas
| Nota | Conteúdo |
|---|---|
| [[Circuito Regulador de Tensão]] | Conversor DC-DC; VID 8 bits (VR/Intel, SVI/AMD); múltiplas tensões (VCORE, secundária, terciária); diagrama controlador PWM → fases; ripple vs. número de fases; ripple vs. frequência de chaveamento; dobradores de fase (f÷2); formato divulgação a+b+c; contar indutores = contar fases; controlador analógico (rápido) vs. digital (preciso); DrMOS (3 MOSFETs em 1 CI); RDS(on) em mΩ; indutores ferro vs. ferrite (saturação/histerese); capacitores eletrolítico < japonês < sólido < tântalo |

### Notas atualizadas
| Nota | O que foi adicionado |
|---|---|
| [[Placa-mãe]] | Link para [[Circuito Regulador de Tensão]] em Ver também |

### Conexões novas
- **VRM +12V ↔ [[Placa-mãe]] conector EPS12V:** conector de 8 pinos da placa-mãe alimenta exclusivamente o VRM do processador
- **VID ↔ [[Processador]]:** processador comunica tensão requerida via código serial — VRM é extensão elétrica do processador
- **ATX12VO +5V VRM ↔ [[Portas de Comunicação]] SATA:** VRM na placa-mãe gera +5V para SATA, mesma função do VRM do processador aplicada a periféricos
- **DrMOS MOSFET RDS(on) ↔ eficiência:** ligação direta entre escolha de componente e consumo/calor — base para comparar placas-mãe com mesmo chipset

---

## Ingestão 75 — 2026-04-12

### Texto Processado
- **Fonte:** Livro de Hardware (clubedohardware.com.br) — Capítulo 39: Portas de Comunicação (SATA 1/2/3 + variantes; SAS 1–4 + conectores backplane/interno/externo + U.2/U.3; USB 1.1–USB4 + nomenclatura + conectores + USB PD; Thunderbolt 1–4 + modos; Ethernet; Wi-Fi/Bluetooth; PS/2; áudio analógico/SPDIF; porta serial)

### Notas criadas
| Nota | Conteúdo |
|---|---|
| [[Portas de Comunicação]] | SATA 1/2/3 (8b/10b, half-duplex, eSATA/SATA Express obsoletos); SAS 1–4 (tabelas backplane SFF-84xx/83xx, interno SFF-84xx/86xx/8621, externo SFF-84xx/86xx/QSFP, U.2/U.3, NL-SAS/iSCSI/SCSIe); USB 1.1–USB4 (tabela completa, nomenclatura caos USB 3.x, conectores Tipo A/B/C/Mini/Micro, USB PD SPR/EPR 15–240 W); Thunderbolt 1–4 (tabela DisplayPort/PCIe/USB/VT-d, modos USB/DP/multifução/Thunderbolt/rede, Thunderspy/VT-d); Ethernet 10M–10G (PHY + HSP); Wi-Fi/Bluetooth M.2/CNVi; PS/2 verde/lilás Super I/O; áudio analógico código de cores + SPDIF coaxial/óptico; porta serial D-Sub 9 |

### Notas atualizadas
| Nota | O que foi adicionado |
|---|---|
| [[Placa-mãe]] | Link para [[Portas de Comunicação]] em Ver também |

### Conexões novas
- **SATA/USB/Ethernet ponto a ponto ↔ [[Barramento]]:** barramento.md já tem tabela com esses tipos; [[Portas de Comunicação]] é a nota de destino com detalhes de cada interface
- **USB4 tunel PCIe/DisplayPort ↔ [[Slots de Expansão]]:** USB4 transporta PCIe pelo mesmo cabo — interface de slots PCIe e porta USB convergem no USB4/Thunderbolt
- **SAS controlador via PCIe x1 ↔ [[Slots de Expansão]]:** controladores SAS são placas de expansão instaladas em slots PCIe
- **HSP (Host Signal Processing) ↔ [[Processador]]:** áudio e rede controlados via chipset = processador faz o trabalho; mesmo princípio do DMA invertido (CPU como processador de sinais)

---

## Ingestão 74 — 2026-04-12

### Texto Processado
- **Fonte:** Livro de Hardware (clubedohardware.com.br) — Capítulo 38: Slots de Expansão (PCIe: pistas/versões/slots físico vs lógico/compatibilidade; Mini PCIe e mSATA obsoletos; M.2: formatos LLCC, configurações B/M, CNVi Intel)

### Notas criadas
| Nota | Conteúdo |
|---|---|
| [[Slots de Expansão]] | PCIe ponto a ponto (não barramento); pistas simplex duplas; tabela versões 1.0–7.0 (8b/10b vs 128b/130b vs PAM4/FLIT; GT/s; largura de banda x1/x4/x8/x16); slot físico ≠ lógico; compatibilidade bidirecional de tamanho e versão; Mini PCIe (PCIe x1 + USB2.0) e mSATA (SATA, conector idêntico ao Mini PCIe); M.2 NGFF (formato LLCC; 2230/2280 mais comuns; configurações B/M; CNVi Intel; versao PCIe depende do controlador) |

### Notas atualizadas
| Nota | O que foi adicionado |
|---|---|
| [[Placa-mãe]] | Link para [[Slots de Expansão]] em Ver também |
| [[Barramento]] | Link para [[Slots de Expansão]] em Ver também |

### Conexões novas
- **PCIe ponto a ponto ↔ [[Barramento]]:** relação já present no Barramento.md agora tem nota dedicada como destino
- **Slot físico ≠ lógico ↔ [[Placa-mãe]] diagrama:** pistas PCIe diretas ao processador vs. via chipset = versões diferentes por slot
- **M.2 CNVi ↔ [[Placa-mãe]]:** conexão proprietária Intel para Wi-Fi já no diagrama da placa-mãe; agora detalhada com implicações para slots M.2
- **M.2 PCIe ↔ [[Memória Flash]]:** SSDs NVMe acessados via PCIe através de slot M.2 — ponte entre hardware de armazenamento e interface de conexão

---

## Ingestão 73 — 2026-04-12

### Texto Processado
- **Fonte:** Livro de Hardware (clubedohardware.com.br) — Capítulo 37: Placas-mãe (componentes; arquitetura chipset/CPU; formatos; soquetes PGA/LGA; 3 compatibilidades; conectores alimentação ATX/ATX12VO/EPS12V; bateria CR2032; ventoinhas 3/4 pinos; RGB analógico/digital; POST codes; camadas de cobre)

### Notas atualizadas
| Nota | O que foi adicionado |
|---|---|
| [[Placa-mãe]] | Reescrita completa: diagrama de conexões CPU↔chipset (DMI/UMI, SPI→ROM/TPM, LPC→Super I/O, CNVi); tabela AMD Zen vs Intel; soquetes PGA/LGA + 3 compatibilidades; tabela formatos ATX→Mobile-ITX; conectores alimentação (24p/10p ATX12VO/EPS12V); bateria CR2032 + falha CMOS; ventoinhas 3/4 pinos PWM; RGB analógico vs digital; POST codes; camadas cobre 1oz/2oz |

### Conexões novas
- **SPI → ROM ↔ [[ROM — Tipos]]:** barramento SPI conecta Flash-ROM NOR ao chipset — formaliza caminho físico do firmware na placa-mãe
- **ATX12VO +12 V apenas ↔ circuito regulador:** placa-mãe ATX12VO gera +5 V internamente para SATA — mesma função do VRM do processador (Cap. 40 pendente), agora aplicada aos periféricos
- **LGA Intel pinos no soquete ↔ PGA AMD pinos no processador:** consequência prática: dano por instalação incorreta recai sobre o soquete (Intel) ou sobre o processador (AMD)
- **3 compatibilidades soquete ↔ [[Firmware]]:** compatibilidade lógica = versão de BIOS — conecta hardware físico ao firmware como requisito de funcionamento

---

## Ingestão 72 — 2026-04-12

### Texto Processado
- **Fonte:** Livro de Hardware (clubedohardware.com.br) — Capítulo 36: Memórias Optane (3D XPoint; PCM; arquitetura seletor+célula; Optane como armazenamento vs RAM; PMEM modos memória/app direct; descontinuação 2021–2022)

### Notas criadas
| Nota | Conteúdo |
|---|---|
| [[Memória Optane]] | 3D XPoint = PCM (Gordon Moore 1970); estado cristalino/amorfo (resistência/refletividade); sem transistor por célula (seletor); Optane vs NAND (latência 1000×, vida útil 1000×, endereçamento por célula); produtos armazenamento (SSD puro/cache/híbrido QLC); PMEM DIMM servidor (modo memória: DRAM como L4 cache; modo app direct: Optane não-volátil persistente); falha de mercado (monopólio, preço, desinformação); descontinuação Intel 2022/Micron 2021 |

### Conexões novas
- **Optane endereçamento por célula ↔ [[Memória Flash]] NOR:** ambos permitem acesso célula individual — Optane é a evolução: não-volátil + leitura/escrita rápida sem o apagamento em bloco da NOR
- **PMEM modo memória ↔ [[Hierarquia de Memória]]:** DRAM como L4 cache para Optane = mesmo princípio hierárquico (cada nível = cache do inferior), mas invertido em relação ao normal
- **Optane não-volátil + acesso aleatório ↔ [[Memória]]:** "mistura" RAM (escrita rápida, endereçável) com ROM (não-volátil) — nova categoria que desafia classificação binária do vault

---

## Ingestão 71 — 2026-04-12

### Texto Processado
- **Fonte:** Livro de Hardware (clubedohardware.com.br) — Capítulo 35: Memórias Flash (arquitetura porta flutuante MOSFET; SLC/MLC/TLC/QLC/PLC; NOR vs NAND; vida útil P/E cycles; retenção de dados)

### Notas criadas
| Nota | Conteúdo |
|---|---|
| [[Memória Flash]] | MOSFET porta flutuante (porta controle + porta flutuante + canal); bits/célula SLC→QLC (estados, velocidade, P/E cycles, consumo); NOR (paralelo, acesso aleatório, XiP, ROM); NAND (série, página/bloco, ECC, armazenamento de massa); tabela NOR vs NAND; vida útil SLC 100k→QLC 1k ciclos; retenção ≥100 anos |

### Conexões novas
- **Porta flutuante MOSFET ↔ [[RAM — Tecnologias e Módulos]]:** DRAM usa capacitor+MOSFET (1T1C); flash usa MOSFET com porta flutuante — mesma família de transistor, princípio de armazenamento diferente
- **NAND acesso por bloco ↔ [[Hierarquia de Memória]]:** NAND não mapeia em endereços como DRAM — obrigatório cópia para RAM; formaliza posição L5 da hierarquia
- **NOR execução direta (XiP) ↔ [[Firmware]]:** firmware UEFI/BIOS executado diretamente da NOR flash sem cópia para RAM
- **P/E cycles TLC/QLC ↔ SSD wear leveling:** vida útil TLC 3.000 / QLC 1.000 cycles → motivo técnico do wear leveling em SSDs (Cap. 48 pendente)

---

## Ingestão 70 — 2026-04-12

### Texto Processado
- **Fonte:** Livro de Hardware (clubedohardware.com.br) — Capítulo 34: ROM (Mask-ROM/PROM/EPROM/EEPROM/Flash-ROM; firmware; BIOS vs UEFI; POST; MBR/GPT; Secure Boot; CMOS RAM; RTC; PCH/ICH)

### Notas criadas
| Nota | Conteúdo |
|---|---|
| [[ROM — Tipos]] | Hierarquia Mask-ROM→Flash-ROM (gravação, apagamento, endereço individual); Flash-ROM tipo NOR; firmware = programa em ROM; ROM em periféricos (GPU, SSD, HDD, consoles); link EEPROM↔SPD |

### Notas atualizadas
| Nota | O que foi adicionado |
|---|---|
| [[Firmware]] | Tabela BIOS vs UEFI expandida (modo real, 1 MiB, MBR 2 TiB, GPT 8 ZiB, Secure Boot, apps na ROM); detalhes POST (7 rotinas); BIOS setup = 3 programas distintos na ROM; seção CMOS RAM (bateria, RTC, PCH/ICH); links para [[ROM — Tipos]] e [[Modos de Operação x86]] |

### Conexões novas
- **CMOS RAM ↔ [[RAM — Tecnologias e Módulos]]:** CMOS é RAM (volátil) mantida por bateria — mesma tecnologia DRAM, uso diferente (configuração de sistema)
- **MBR 32 bits ↔ [[Modos de Operação x86]]:** limite MBR 2 TiB vem do endereçamento 32 bits; BIOS roda em modo real (1 MiB) — limite físico de arquitetura
- **EEPROM SPD ↔ Flash-ROM BIOS:** ambas são ROM elétricas; EEPROM permite byte individual; Flash-ROM apaga tudo — trade-off flexibilidade vs velocidade
- **GPT 64 bits ↔ Paginação x86:** UEFI usa endereçamento 64 bits (8 ZiB), análogo à extensão de espaço endereçável do Sunny Cove (57 bits) documentada em [[Microarquitetura Intel Família 6 Parte 3]]

---

## Ingestão 69 — 2026-04-12

### Texto Processado
- **Fonte:** Livro de Hardware (clubedohardware.com.br) — Capítulo 33: RAM (SRAM/DRAM; SDRAM; DDR1–5; LPDDR1–5X; GDDR1–6X; HBM1/2/2E; módulos DIMM/SODIMM/RDIMM/LRDIMM/ECC; densidade; largura de banda; latência; canais)

### Notas criadas
| Nota | Conteúdo |
|---|---|
| [[RAM — Tecnologias e Módulos]] | SRAM (flip-flop) vs DRAM (capacitor+MOSFET); arquitetura SDRAM (bancos, pinos DQ/A/BA/CK/ODT, comandos ACT/READ/WRITE/PRE/REF, tRCD, CL, rajada); tabelas DDR1–5 (pré-busca 2n→16n, grupos bancos, tensões, CRC, ECC chip); LPDDR1–5X (DVS/DVFS, WCK/RDQS clock, relação 2:1/4:1); GDDR1–6X (WCK, QDR/ODR, PAM4, modo clamshell, EDC); HBM1/2/2E (TSV, 128 bits/canal, até 8 canais/pilha, Pseudo Channel, 1,64 TB/s exemplo); módulos DIMM/SODIMM/RDIMM/LRDIMM; ECC SECDED; SPD/XMP/EXPO; canais dual/quad; cálculo largura de banda e latência absoluta |

### Notas atualizadas
| Nota | O que foi adicionado |
|---|---|
| [[Memória]] | Link para [[RAM — Tecnologias e Módulos]] na seção DRAM |

### Conexões novas
- **SRAM cache ↔ DRAM principal:** gap velocidade/custo justifica toda a hierarquia de cache — formaliza relação entre [[Cache]] e [[RAM — Tecnologias e Módulos]]
- **HBM2E ↔ Golden Cove:** [[Microarquitetura Intel Família 6 Parte 3]] usa HBM2E em servidor — now documentado com detalhes técnicos (TSV, 1,64 TB/s)
- **K8 controlador integrado ↔ SDRAM:** [[Processadores AMD Família Hammer]] integrou controlador DDR em 2003; Nehalem em 2008 — impacto direto na arquitetura de barramento SDRAM
- **GDDR5 + Jaguar ↔ Bobcat:** [[Processadores AMD Família Bobcat]] no PS4/Xbox One usa GDDR5 — modo clamshell e largura de banda agora explicados
- **DDR dual channel > DDR5 single:** largura de banda depende de canais, não só de geração — DDR4-2133 ×2 canais (34 GB/s) > DDR4-3200 ×1 canal (25,6 GB/s)

---

## Ingestão 63 — 2026-04-12

### Texto Processado
- **Fonte:** Livro de Hardware (clubedohardware.com.br) — Capítulo 27: Processadores Intel Híbridos (Lakefield/Alder Lake/Raptor Lake; P+E cores; restrições de instruções; L3 compartilhado)

### Notas criadas
| Nota | Conteúdo |
|---|---|
| [[Processadores Intel Híbridos]] | Conceito P+E (análogo ARM big.LITTLE, primeira vez em x86); tabela Lakefield/Alder/Raptor com µarqs; tabela restrições de instruções (Tremont sem AVX → Lakefield sem AVX; Gracemont sem AVX-512 → Alder/Raptor sem AVX-512); núcleos E sem HT; L3 compartilhado P+E; Lakefield (2 modelos, fora sequência, 8GiB LPDDR4X integrada, experimental); Alder Lake 12ª geração; Raptor Lake 13ª geração; Celeron/Pentium Gold híbridos |

### Notas atualizadas
| Nota | O que foi adicionado |
|---|---|
| [[Microarquitetura Intel Família Atom]] | Link para [[Processadores Intel Híbridos]] |
| [[Microarquitetura Intel Família 6 Parte 3]] | Link para [[Processadores Intel Híbridos]] |

### Conexões novas
- **Gracemont sem AVX-512 ↔ Golden Cove cliente:** Gracemont é a razão direta do AVX-512 desabilitado nos Core i 12ª/13ª geração
- **Lakefield 8GiB integrada ↔ [[Cache]]:** LPDDR4X on-package = antecessor do HBM2E do Golden Cove servidor

---

## Ingestão 62 — 2026-04-12

### Texto Processado
- **Fonte:** Livro de Hardware (clubedohardware.com.br) — Capítulo 26: Intel Família Atom (Bonnell→Gracemont; SoFIA; execução em ordem→OoO; BGA; híbridos P+E)

### Notas criadas
| Nota | Conteúdo |
|---|---|
| [[Microarquitetura Intel Família Atom]] | Tabela µarqs 2008–2021; Bonnell (em ordem, HT, pipeline 16/19 estágios, marcadores L1-I 36KiB, 2 portas); Silvermont (OoO+PRF, módulo 2 núcleos, fila reabilitação, 3 agendadores); Goldmont (3µops, 3 portas, AGU OoO, SMEP/SMAP); Goldmont Plus (4µops, 4ª porta desvios, radix-1024, módulo 4 núcleos); Tremont (6 instr dec, 10 portas individuais, ROB 208, L3 LLC, GFNI/TME); Gracemont (OD-ILD, 5µops, 17 portas, ROB 256, L1-I 64KiB, AVX2/FMA3 primeira vez no Atom) |

### Notas atualizadas
| Nota | O que foi adicionado |
|---|---|
| [[Microarquitetura Intel Família 6 Parte 3]] | Link para [[Microarquitetura Intel Família Atom]] |

### Conexões novas
- **Gracemont AVX2 ↔ [[Instruções SIMD]]:** primeira µarq Atom com AVX — razão do AVX-512 desabilitado nos híbridos
- **Tremont L3 LLC ↔ [[Cache]]:** primeira vez que µarq Atom ganha L3 (só Celeron/Pentium Silver)

---

## Ingestão 61 — 2026-04-12

### Texto Processado
- **Fonte:** Livro de Hardware (clubedohardware.com.br) — Capítulo 25: Intel Família 6 Parte 3 (Nehalem→Golden Cove; Core i 1ª–13ª geração; tick-tock→PAO; DSB; PRF; DMI/QPI/UPI; arquitetura em anel; Xeon Escalonável)

### Notas criadas
| Nota | Conteúdo |
|---|---|
| [[Microarquitetura Intel Família 6 Parte 3]] | Tabelas tick-tock e PAO; Core i nomenclatura; Nehalem (controlador memória integrado, DMI/QPI, LSD→IDQ, HT, Turbo Boost); Sandy Bridge (DSB vs trace cache, RRF→PRF, anel, AVX); Haswell (8 portas, FMA/AVX2/BMI); Skylake (5 dec, DSB 6µops, Speed Shift, UPI, AVX-512 servidor); Sunny Cove (57b paginação, 10 portas, AVX-512 universal); Golden Cove (32B/ciclo, DSB 4.096µops, 12 portas, ROB 512, AMX/CXL/HBM2E); tabela P6→Golden Cove; Xeon Escalonável |

### Notas atualizadas
| Nota | O que foi adicionado |
|---|---|
| [[Microarquitetura Core e Processadores Intel Família 6 Parte 2]] | Link para [[Microarquitetura Intel Família 6 Parte 3]] |

### Conexões novas
- **DSB ↔ Trace Cache Netburst:** µops individuais sem duplicidade vs. na ordem de execução com repetições
- **PRF ↔ [[Desempenho do Processador]]:** renomeamento sem carregar dados nas µops — habilita AVX-512 sem alargar caminhos
- **Sunny Cove 57b ↔ [[Paginação x86]]:** primeira extensão do espaço de endereçamento desde PAE

---

## Ingestão 64 — 2026-04-12

### Texto Processado
- **Fonte:** Livro de Hardware (clubedohardware.com.br) — Capítulo 28: AMD Família K7 (Athlon, Athlon XP, Duron, Sempron, Athlon MP, portáteis; CPUID família 6; barramento DDR MT/s; nomenclatura PR)

### Notas criadas
| Nota | Conteúdo |
|---|---|
| [[Processadores AMD Família K7]] | CPUID família 6 AMD (razão histórica); barramento DDR tabela MT/s; nomenclatura PR explicada; Athlon (L1 64+64KiB, MMX/Enhanced 3DNow!, Slot A→soquete 462, codinomes Argon/Pluto/Orion/Thunderbird); Athlon XP (SSE+3DNow! Professional, PR, codinomes Palomino/Thoroughbred/Thorton/Barton); Duron (low-cost, sem PR, L2 64KiB, Spitfire/Morgan/Applebred); Sempron K7 (Athlon XP renomeado, aviso K7≠K8); portáteis (Mobile Athlon 4, Mobile Athlon XP, Athlon XP-M/soquete 563); Mobile Duron; Athlon MP (SMP 2 vias) |

### Notas atualizadas
| Nota | O que foi adicionado |
|---|---|
| [[Processadores x86 Família 5]] | Link para [[Processadores AMD Família K7]] |

### Conexões novas
- **K7 barramento DDR ↔ Netburst QDR:** dois fabricantes diferentes inflando clock externo — AMD ×2, Intel ×4; ambos expressos corretamente em MT/s
- **Nomenclatura PR K7 ↔ Cyrix/K5 PR:** mesma estratégia da família 5 (Cyrix 6x86-PR, K5-PR) retorna no K7 para combater Pentium 4

---

## Ingestão 68 — 2026-04-12

### Texto Processado
- **Fonte:** Livro de Hardware (clubedohardware.com.br) — Capítulo 32: AMD Família Zen (Zen/Zen+/Zen2/Zen3/Zen3+/Zen4; CCX; Zeppelin; CCD+IOD chiplet; Infinity Fabric; Precision Boost/XFR/PBO; Ryzen/Threadripper/EPYC; consoles Zen 2)

### Notas criadas
| Nota | Conteúdo |
|---|---|
| [[Processadores AMD Família Zen]] | Tabela µarqs Zen–Zen4; pipeline Zen (IBQ 20e, 4 decoders, Op Cache 2048e, SEM, NSQ, ROB 192, PRF INT 168/FP 160, 6 ag INT+1 FP, retirada 8/ciclo); exec units Zen (FMUL/FADD/FCVT/FDIV/VADD/VMUL/VSHUF/VSHIFT/AES/CLMUL); estrutura modular CCX/Zeppelin; chiplet Zen2 CCD+IOD; CCX unificado Zen3 (8 núcleos); tabelas PCIe (Zen/Zen2/Zen4), USB (Zen/Zen2/Zen4); Precision Boost/XFR/PBO/PBO2; Zen2/Zen3/Zen3+/Zen4 diffs; tabela processadores desktop/portátil Ryzen; Threadripper; Athlon; EPYC Naples/Rome/Milan; NUMA/UMA |

### Notas atualizadas
| Nota | O que foi adicionado |
|---|---|
| [[Processadores AMD Família Bulldozer]] | Link para [[Processadores AMD Família Zen]] |

### Conexões novas
- **Op Cache Zen ↔ [[Microarquitetura Intel Família 6 Parte 3]]:** DSB Intel (Sandy Bridge) vs Op Cache Zen — ambos entregam µops sem re-decodificação; Intel usa DSB desde 2011, AMD chega em 2017
- **Chiplet CCD+IOD ↔ [[Fabricação de Chips]]:** separar núcleos (7nm) de I/O (12nm) → maior yield, menor custo — técnica que AMD usou como vantagem sobre Intel monolítico
- **Zen CCX L3 compartilhado ↔ Zen3 CCX único:** Zen3 remove barreira Infinity Fabric intra-CCD — todos 8 núcleos no mesmo L3 sem overhead de latência inter-CCX
- **EPYC 64c SMP 2-vias ↔ Opteron 16c SMP 8-vias:** mesmo 128 threads total; EPYC muito mais econômico e compacto

---

## Ingestão 67 — 2026-04-12

### Texto Processado
- **Fonte:** Livro de Hardware (clubedohardware.com.br) — Capítulo 31: AMD Família Bobcat (Bobcat/Jaguar/Puma; baixo consumo; OoO vs. Atom em ordem; power/clock gating; L2 clock/2; consoles PS4/Xbox One; AM1; encerrada 2015)

### Notas criadas
| Nota | Conteúdo |
|---|---|
| [[Processadores AMD Família Bobcat]] | CPUID 14h/16h; Bobcat (64-bit interno, 2 macros/linha, L2 512KiB clock/2, LAGU/SAGU, SSE–SSSE3, PCIe 1.1 UMI 1GB/s, iGPU DX11 TeraScale2); Jaguar (128-bit interno, PRF FP 72×128b, +DIV, +VALU/VIMUL, AVX/SSE4.x/AES-NI, L2 1-2MiB compartilhado, PCIe 2.0 UMI 2GB/s, AM1, PS4/Xbox One); Puma (+RDRAND, PSP/TPM 2.0, UVD 4.2); Puma+ (UVD 6.0/VCE 3.1); tabela processadores (Série A/E/C/Z/Athlon/Sempron/Opteron Kyoto); tabela comparativa µarqs |

### Notas atualizadas
| Nota | O que foi adicionado |
|---|---|
| [[Processadores AMD Família Hammer]] | Warning: Zacate/Ontario são Bobcat (14h), não K10.5 — nome "Série E" reutilizado |
| [[Processadores AMD Família Bulldozer]] | Link para [[Processadores AMD Família Bobcat]] |

### Conexões novas
- **Bobcat OoO ↔ [[Microarquitetura Intel Família Atom]]:** Bobcat (2011) era OoO quando Atom ainda era em ordem; Intel corrigiu no Silvermont (2013)
- **Puma PSP/TPM ↔ [[Processadores AMD Família Bulldozer]]:** mesmo PSP/TPM 2.0 introduzido no Excavator — convergência de segurança entre as duas famílias
- **Zacate/Ontario ↔ Hammer K10.5:** erro na nota anterior corrigido com warning — "Série E" é nome usado em Bobcat E a K10.5 APU

---

## Ingestão 66 — 2026-04-12

### Texto Processado
- **Fonte:** Livro de Hardware (clubedohardware.com.br) — Capítulo 30: AMD Família Bulldozer (Bulldozer/Piledriver/Steamroller/Excavator; módulo compartilhado; soquetes AM3+/FM2/FM2+/AM4; FX/Série A/Opteron; polêmica "cores")

### Notas criadas
| Nota | Conteúdo |
|---|---|
| [[Processadores AMD Família Bulldozer]] | CPUID 15h; conceito de módulo (2 INT + 1 FP + 1 front-end compartilhados); polêmica judicial 2015 AMD; tabela µarqs Bulldozer→Excavator+; pipeline (4µops/ciclo, fusão compare+branch, PRF 96/160 entradas, fila decoded Steamroller); unidades exec (FMAC/IMAC/MMX/XBAR/FSTOR, 128-bit FP, store 256-bit 3→2 ciclos); tabela soquetes C32/G34/AM3+/FM2/FM2+/AM4; processadores FX/FirePro/Série A/Athlon/Sempron/Série E/Opteron (codinomes+anos+processo); tabela comparativa 4 µarqs |

### Notas atualizadas
| Nota | O que foi adicionado |
|---|---|
| [[Processadores AMD Família Hammer]] | Link para [[Processadores AMD Família Bulldozer]] |

### Conexões novas
- **Módulo Bulldozer ↔ [[Desempenho do Processador]]:** módulo não é dual-core real; FP compartilhado = gargalo; PRF viabiliza execução OoO de FP em módulo compartilhado
- **Fila decoded Steamroller ↔ [[Microarquitetura Intel Família 6 Parte 3]]:** LSD Intel (Nehalem, 2008) chega no AMD só em 2013 com Steamroller
- **Excavator PSP/TPM ↔ [[Instruções Adicionais x86]]:** RDRAND habilitado por RNG no PSP; TPM 2.0 integrado no die

---

## Ingestão 65 — 2026-04-12

### Texto Processado
- **Fonte:** Livro de Hardware (clubedohardware.com.br) — Capítulo 29: AMD Família Hammer (K8/K10/K10.5; Athlon 64; Phenom; Opteron; Turion; APU Série A; HyperTransport; controlador de memória integrado; 64 bits; AMD-V+RVI; SSE4a; Wide FP Accelerator)

### Notas criadas
| Nota | Conteúdo |
|---|---|
| [[Processadores AMD Família Hammer]] | CPUID F/10h/12h; tabela soquetes (754/939/940/AM2/AM2+/AM3/FM1/C32/G34/F); tabela HyperTransport 1.x/2.0/3.0/3.1 em MT/s; K8 (primeiro 64-bit x86, primeiro controlador memória integrado, macroinstruções DirectPath/VectorPath, linhas de 3, IFFRF, pipeline 12/17 estágios, 3ALU+3AGU+FP, problema divisor memória); K10 (32B busca, Wide FP Accelerator 128-bit, SSE4a, RVI, Turbo Core, DDPM, ABM, HT 3.0, fix divisor); K10.5 (ROB 84 µops, INT DIV, APU/GPU TeraScale2 DX11, PCIe 2.0, UMI, FM1); processadores desktop/portátil/servidor incluindo Opteron com numeração SMP e Magny-Cours MCM |

### Notas atualizadas
| Nota | O que foi adicionado |
|---|---|
| [[Processadores AMD Família K7]] | Link para [[Processadores AMD Família Hammer]] |

### Conexões novas
- **K8 controlador memória integrado ↔ Intel Nehalem:** Intel integrou o controlador de memória no Nehalem (2008) — AMD fez isso em 2003 com o K8
- **K10.5 APU ↔ [[Cache]]:** GPU integrada compartilha L2 com CPU — nova dinâmica de hierarquia de cache
- **HyperTransport MT/s ↔ K7 DDR FSB MT/s:** mesma convenção de notação DDR (×2); HT evolui até 3.200 MT/s no HT 3.1

---

> Ingestões anteriores (≤63) removidas para compactar. Próxima ingestão: 66.
