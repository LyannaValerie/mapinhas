---
title: Entrada e Saída no Linux
aliases:
  - E/S no Linux
  - IO no Linux
  - módulos no Linux
  - drivers Linux
tags:
  - computação/sistemas
source: "Sistemas Operacionais Modernos, cap. 10"
created_by: Codex
---

# Entrada e Saída no Linux

## Definição

Subsistema que organiza dispositivos, drivers, arquivos especiais, rede e chamadas de sistema de E/S sob uma interface uniforme. Todo dispositivo parece um arquivo.

## Papel técnico

Linux preserva a tradição UNIX: todos os dispositivos são integrados ao sistema de arquivos como **arquivos especiais** em `/dev`. A aplicação usa `open`, `read`, `write`, `ioctl` e `select`; o núcleo encaminha para drivers, pilhas de protocolo ou sistemas de arquivos.

## Relações (SPO)

- E/S no Linux -> usa -> descritores de arquivo
- Driver -> controla -> dispositivo
- Módulo do núcleo -> adiciona -> driver ou funcionalidade
- Arquivo especial (/dev) -> representa -> dispositivo
- Socket -> representa -> comunicação de rede
- `ioctl` -> envia -> comandos específicos de dispositivo
- Disciplina de linha -> processa -> fluxo de caracteres do terminal

## Arquivos Especiais: Bloco e Caractere

Cada dispositivo tem nome em `/dev` (ex: `/dev/hd1` para disco, `/dev/lp` para impressora). Chamadas de sistema usuais funcionam:

```sh
cp file /dev/lp   # imprime file (se tiver permissão)
```

| Tipo | Característica | Exemplos |
|------|---------------|---------|
| **Bloco** | Blocos numerados, acesso aleatório | Discos, SSDs |
| **Caractere** | Fluxo de bytes, sem acesso aleatório | Teclado, impressora, mouse, terminal |

## Disciplina de Linha (Terminal)

Para dispositivos de caractere interativos, uma **disciplina de linha** fica entre o driver e o processo:
- Modo processado: expande tabs, converte CR→LF, trata backspace, configura tecla de interrupção
- Modo bruto: bypass da disciplina — útil para GUIs e transmissão de dados binários

Configurável via chamada de sistema especial (customização de CTRL-C, eco, etc.).

## Rede (Sockets BSD)

Introduzida pelo Berkeley UNIX, usada pelo Linux:

```c
fd = socket(domain, type, protocol);
bind(fd, &addr, len);     // servidor
connect(fd, &addr, len);  // cliente
read(fd, buf, len);
write(fd, buf, len);
```

3 tipos de socket:
1. **Stream** (TCP): bytes contíguos, sem limites de pacote, confiável e ordenado
2. **Datagrama** (tipo 2): preserva limites de pacote (5 writes de 512B = 5 reads)
3. **Raw**: acesso de baixo nível à rede; para aplicações em tempo real

Buffer interno `skbuff`: estrutura de dados do pacote na pilha de rede. Cabeçalhos de protocolos são adicionados/removidos à medida que sobem/descem a pilha.

## Módulos do Núcleo

O Linux permite carregar drivers e outras funcionalidades em tempo de execução sem reinicializar:

```sh
insmod driver.ko    # insere módulo
rmmod driver        # remove módulo
```

Ao carregar um módulo:
1. Realocação dinâmica do código do módulo
2. Verificação de recursos disponíveis (ex: nível de IRQ)
3. Configuração de vetores de interrupção necessários
4. Atualização da tabela de troca do driver
5. Execução da inicialização específica do dispositivo

## Chamadas de Sistema para E/S

| Chamada | Descrição |
|---------|-----------|
| `open(file, flags)` | Abre arquivo ou dispositivo |
| `read(fd, buf, n)` | Lê bytes |
| `write(fd, buf, n)` | Escreve bytes |
| `ioctl(fd, cmd, arg)` | Comando específico do dispositivo |
| `select(n, rd, wr, ex, tv)` | Espera eventos em múltiplos descritores |
| `close(fd)` | Fecha descritor |

## Ver também

- [[Entrada e Saída em Sistemas Operacionais]]
- [[Driver]]
- [[Descritores de Arquivo UNIX]]
- [[Sistema de Arquivos Linux]]
- [[Interrupção]]
