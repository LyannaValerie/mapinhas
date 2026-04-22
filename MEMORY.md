# MEMORY — Knowledge Vault Session Log

---

## Ingestão 83 — 2026-04-21

### Texto Processado
- **K&R C Apêndices A/B/C** — Reference Manual, Standard Library, Summary of Changes

### Notas criadas
| Nota | Conteúdo |
|---|---|
| [[Qualificadores de Tipo em C]] | `const` (imutável após init, ponteiros const, parâmetros); `volatile` (hardware registers, sig handlers, threads); combinação `const volatile` |
| [[Lvalue e Objeto em C]] | Objeto = região nomeada de armazenamento; lvalue = expressão que refere a objeto; `*E` é lvalue; lvalue não-modificável (const, array, struct com const); rvalue |
| [[Biblioteca ctype.h]] | Tabela `is*` (isalnum/isalpha/iscntrl/isdigit/isgraph/islower/isprint/ispunct/isspace/isupper/isxdigit); `tolower`/`toupper`; cast obrigatório para `unsigned char` |
| [[Biblioteca string.h]] | Cópia (strcpy/strncpy/strcat/strncat); comparação (strcmp/strncmp); busca (strchr/strrchr/strspn/strcspn/strpbrk/strstr/strlen/strerror/strtok); memória (memcpy/memmove/memcmp/memchr/memset) |
| [[Biblioteca math.h]] | Trig (sin/cos/tan/asin/acos/atan/atan2); hiperbólicas; exp/log/log10/pow/sqrt; arredondamento (ceil/floor/fabs); decomposição (ldexp/frexp/modf/fmod); EDOM/ERANGE/HUGE_VAL; flag `-lm` |
| [[Biblioteca stdlib.h em C]] | Conversão (atoi/atof/atol sem erro; strtod/strtol/strtoul com endp); rand/srand/RAND_MAX; qsort/bsearch com cmp; abort/exit/atexit; system/getenv |
| [[Sinais em C]] | `signal(sig, handler)`; `raise(sig)`; SIG_DFL/SIG_IGN/SIG_ERR; tabela SIGABRT/SIGFPE/SIGILL/SIGINT/SIGSEGV/SIGTERM; `volatile sig_atomic_t`; restrições dentro de handlers; diferença ANSI vs POSIX |
| [[Biblioteca time.h]] | `time_t`/`clock_t`/`struct tm` (campos); clock/time/difftime/mktime; asctime/ctime/gmtime/localtime (buffers estáticos); strftime com tabela de especificadores |
| [[Limites de Implementação C]] | limits.h: tabela CHAR_BIT/CHAR_MAX/INT_MAX/LONG_MAX/etc.; float.h: FLT_DIG/FLT_EPSILON/FLT_MAX/DBL_DIG/DBL_EPSILON/DBL_MAX; INT_MIN ≠ -INT_MAX em complemento de 2 |
| [[ANSI C — Mudanças]] | Novos keywords (const/volatile/signed/void/enum); protótipos com tipos; `void *` sem cast; concatenação de strings adjacentes; trigrafos; `#`/`##`/`#elif`/`defined`; biblioteca padronizada; `strtol`/`memmove`/`strftime` novos |

### Conexões criadas
- Qualificadores ↔ Lvalue e Objeto (const cria lvalue não-modificável)
- Sinais em C ↔ Sinais OS (interface ANSI vs POSIX — complementares)
- stdlib.h ↔ Alocação Dinâmica (nota separada para malloc/free)
- stdlib.h ↔ Biblioteca time.h (srand usa time(NULL))
- Limites ↔ Tipos de Dados e Aritmética Inteira (INT_MIN overflow)

### Lacunas pendentes
- `<assert.h>` (B.6): apenas macro `assert()` + `NDEBUG` — não criada (conteúdo mínimo)

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

> Ingestões anteriores (≤80) removidas para compactar. Próxima ingestão: 84.
