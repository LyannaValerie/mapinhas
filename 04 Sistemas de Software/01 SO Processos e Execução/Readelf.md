---
title: Readelf
aliases:
  - readelf
  - inspeção ELF
  - metadados ELF
tags:
  - computação/sistemas
source: "Tu, Do Hoang. Operating Systems: From 0 to 1"
created_by: Codex
---

# Readelf

%% criado pelo Codex %%

## Definição

`readelf` reconhece e exibe metadados de binários no formato [[Formato ELF]], tanto [[Arquivo Objeto|arquivos objeto]] quanto executáveis.

## Uso

O ELF funciona como uma tabela de conteúdo do binário: descreve seções, segmentos, símbolos e endereços necessários para inspeção, linkedição, carregamento e execução.

Comandos típicos:

```sh
readelf -h arquivo
readelf -S arquivo
readelf -l arquivo
readelf -s arquivo
```

## Relações

- `-h` examina [[Cabeçalho ELF]].
- `-S` examina [[Tabela de Cabeçalhos de Seção ELF]].
- `-l` examina [[Tabela de Cabeçalhos de Programa ELF]].
- Complementa [[Objdump]] em análise estática.

