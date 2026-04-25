---
title: Objdump
aliases:
  - objdump
  - desmontagem de binário
  - disassembly
tags:
  - computação/sistemas
source: "Tu, Do Hoang. Operating Systems: From 0 to 1"
created_by: Codex
---

# Objdump

%% criado pelo Codex %%

## Definição

`objdump` é ferramenta de inspeção estática usada para examinar código de máquina, assembly gerado e conteúdo de [[Arquivo Objeto|arquivos objeto]] ou executáveis.

## Uso

Em código baixo nível, o compilador gera assembly a partir de C, mas nem toda capacidade da plataforma de hardware aparece diretamente em linguagem de alto nível. Ler assembly é necessário para depurar código que toca recursos específicos de hardware.

Com opções como `-D`, `-S`, `-M intel` e `--no-show-raw-insn`, `objdump` ajuda a comparar código fonte, instruções geradas e layout de dados compilados.

## Relações

- Complementa [[Readelf]]: `objdump` foca instruções e conteúdo; `readelf` foca metadados ELF.
- Apoia estudo de [[Linguagem Assembly]] e [[Linguagem de Máquina]].
- Útil para entender saída de [[Sistema de Compilação]].

