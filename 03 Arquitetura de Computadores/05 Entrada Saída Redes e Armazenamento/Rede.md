---
title: Rede
aliases:
  - network
  - rede de computadores
  - adaptador de rede
  - cliente-servidor
  - Internet
tags:
  - computação/fundamentos
date: 2026-04-07
---

%% Navegação hierárquica gerada pelo Codex %%
> [!info] Navegação
> MOC pai: [[MOC - Arquitetura de Computadores]]

# Rede

## Definição
Do ponto de vista de um sistema individual, a rede é **apenas mais um dispositivo de E/S** — uma extensão da [[Hierarquia de Memória|hierarquia de armazenamento]] que alcança outros computadores. O [[Sistema Operacional]] trata dados de rede da mesma forma que dados de disco: sequências de bytes movidas entre a [[Memória]] principal e o adaptador de rede.

## Relações (SPO)
- Rede → é vista como → dispositivo de E/S pelo sistema
- Rede → conecta → sistemas individuais entre si
- Rede → abstrai → pela abstração de [[Arquivo]] do [[Sistema Operacional]] (Unix I/O)
- Rede → possibilita → modelo cliente-servidor

---

## Rede como dispositivo de E/S

Quando o sistema copia uma sequência de bytes da [[Memória]] principal para o **adaptador de rede**, os dados fluem pela rede até outra máquina — em vez de ir para o disco local. Da mesma forma, o sistema pode receber dados enviados por outras máquinas e copiá-los para a memória principal.

```
CPU chip
    │
    ▼
Memória principal ──→ adaptador de rede ──→ [ rede ] ──→ outro computador
```

O adaptador de rede conecta-se ao [[Barramento|barramento de E/S]], exatamente como o controlador de disco. O acesso se dá via [[Chamada do Sistema]] — a abstração de [[Arquivo]] do Unix cobre tanto arquivos em disco quanto conexões de rede.

---

## Modelo Cliente-Servidor

A forma típica de comunicação via rede é o modelo **cliente-servidor**:

| Papel | Descrição |
|---|---|
| **Cliente** | Inicia a conexão; envia requisição; recebe resposta |
| **Servidor** | Aguarda conexões; processa a requisição; envia resposta |

### Exemplo: telnet rodando `hello` remotamente

```
1. Usuário digita "hello" no cliente telnet (terminal local)
2. Cliente telnet envia string "hello" ao servidor telnet (máquina remota)
3. Servidor telnet passa "hello" ao shell remoto → shell executa o programa ./hello
4. hello imprime "hello, world\n" → saída retorna ao servidor telnet
5. Servidor telnet envia a string de saída de volta ao cliente
6. Cliente exibe "hello, world\n" no terminal local
```

Esse padrão de troca entre cliente e servidor é a base de **todas** as aplicações de rede: e-mail, mensagens instantâneas, Web, FTP, SSH.

---

## Internet e aplicações

Com o advento de redes globais como a Internet, copiar informação de uma máquina para outra tornou-se um dos usos mais importantes dos sistemas computacionais. Todas as aplicações abaixo se baseiam na capacidade de copiar bytes pela rede:

- E-mail
- Mensagens instantâneas (chat)
- World Wide Web
- FTP (transferência de arquivos)
- Telnet / SSH (terminal remoto)

---

## Ver também
- [[Sistema Operacional]] — abstração de arquivo cobre dispositivos de rede (Unix I/O)
- [[Barramento]] — adaptador de rede conecta-se ao barramento de E/S
- [[Memória]] — dados de rede são copiados de/para a memória principal
- [[Hierarquia de Memória]] — rede como extensão da hierarquia além do armazenamento remoto (L6)
- [[DMA]] — transferências entre adaptador e memória podem usar DMA
- [[Transmissão de Dados]] — Ethernet e conexões TCP operam em full-duplex; o meio físico (cabo, fibra, ondas de rádio) é o "meio" da transmissão
