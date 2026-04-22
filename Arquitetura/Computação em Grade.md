---
title: Computação em Grade
aliases:
  - grid computing
  - grade computacional
  - OGSA
  - Globus Toolkit
  - computação distribuída
tags:
  - computação/arquitetura
date: 2026-04-21
---

# Computação em Grade

## Definição

Forma de computação paralela **fracamente acoplada** onde computadores geograficamente distribuídos, conectados via **Internet**, cooperam para resolver problemas de grande escala. Extremidade mais fraca do espectro de acoplamento: [[Concorrência e Paralelismo#Arquiteturas Paralelas de Múltiplos Processadores|chip → coprocessador → multiprocessador → multicomputador → grade]].

## Relações (SPO)
- Grade → conecta → computadores heterogêneos via Internet
- OGSA → padroniza → serviços de infraestrutura para grades
- Globus Toolkit → implementa → padrões OGSA em software open-source
- Grade → usa → WSDL para descrição de serviços (reaproveitando padrões Web)
- Grade → diferencia-se de → cluster por escala geográfica e heterogeneidade

---

## Motivação

Problemas científicos e industriais exigem capacidade computacional além de qualquer máquina individual:

- Simulação astronômica (Big Bang → estado atual do universo)
- Modelagem climática e previsão
- Dobramento de proteínas e pesquisa farmacêutica
- Testes de aerodinâmica sem protótipos físicos

Ideia central: computadores ociosos ao redor do mundo somam capacidade de processamento equivalente a supercomputadores, se bem coordenados.

---

## Arquitetura: OGSA

**Open Grid Services Architecture** — framework criado pelo Global Grid Forum para padronizar serviços de grade.

Reutiliza padrões existentes onde possível (ex: WSDL para descrição de serviços). Oito categorias de serviços em padronização:

| Categoria | Função |
|---|---|
| **Infraestrutura** | Habilita serviços fundamentais de grade |
| **Gerenciamento de recursos** | Aloca CPUs, memória, armazenamento |
| **Dados** | Acesso e movimentação de grandes volumes de dados |
| **Contexto** | Informações de sessão e ambiente de execução |
| **Informação** | Descoberta e monitoramento de recursos |
| **Auto-gerenciamento** | Adaptação dinâmica a falhas e variações de carga |
| **Segurança** | Autenticação, autorização, criptografia entre domínios |
| **Execução** | Submissão, escalonamento e controle de jobs |

---

## Globus Toolkit

Implementação open-source de referência para grades OGSA.

- Fornece: autenticação (GSI), transferência de dados (GridFTP), execução remota (GRAM), informação (MDS)
- Base da maioria dos projetos de grade científica
- Usado em LHC (CERN), TeraGrid (NSF, EUA), EGEE (Europa)

---

## Grade vs. Cluster vs. MPP

| Aspecto | MPP | Cluster | Grade |
|---|---|---|---|
| Localização | Sala de máquinas única | Mesmo datacenter | Geograficamente distribuída |
| Rede | Proprietária (μs) | Ethernet comercial (10–100 μs) | Internet (ms) |
| Heterogeneidade | Homogêneo | Normalmente homogêneo | Heterogêneo |
| Administração | Centralizada | Centralizada | Múltiplos domínios |
| Latência de comunicação | ~1 μs | ~10–100 μs | ~10–100 ms |
| Custo | Altíssimo | Moderado | Baixo (infraestrutura existente) |

---

## Escalabilidade em Topologia de Grade 2D

Propriedade favorável da topologia em **grade n×n** para interconexão de CPUs:

```
Barramento (não escalável):      Grade 2D (escalável):
CPU─CPU─CPU─...─CPU              CPU─CPU─CPU
        │                         │   │   │
     Barramento                  CPU─CPU─CPU
                                  │   │   │
  b/n → largura de              CPU─CPU─CPU
  banda por CPU cai
```

- Barramento: largura de banda por CPU = b/n — **degrada com n**
- Grade: adicionar CPUs adiciona enlances → largura de banda agregada por CPU **mantém ou cresce**
- Custo: diâmetro cresce como √n (latência no pior caso = 2(n−1) para grade n×n)

---

## Ver também
- [[Multicomputadores e Clusters]] — nível anterior no espectro de acoplamento
- [[Concorrência e Paralelismo]] — taxonomia Flynn, espectro de paralelismo
- [[Redes de Interconexão]] — topologias; grade 2D como topologia de interconexão
- [[Lei de Amdahl]] — limite de speedup mesmo em grades
