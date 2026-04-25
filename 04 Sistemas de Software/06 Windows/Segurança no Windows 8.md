---
title: Segurança no Windows 8
aliases:
  - segurança do Windows
  - descritor de segurança
  - token de acesso
  - ACL no Windows
tags:
  - computação/sistemas
  - sistemas-operacionais/windows
  - segurança
source: "Sistemas Operacionais Modernos, cap. 11"
created_by: Codex
---

# Segurança no Windows 8

## Definição

Conjunto de mecanismos da arquitetura NT para autenticação, autorização, auditoria e proteção de objetos. Integrado ao [[Gerenciador de Objetos do Windows]].

## Identificadores de segurança (SID)

Todo usuário e grupo possui um **SID**: número binário único globalmente, com cabeçalho fixo e componente aleatório longo. O Windows usa SIDs como identidade permanente — nomes de usuário são apenas aliases.

## Token de acesso

Cada processo carrega um **token de acesso** atribuído no login:

| Campo | Conteúdo |
|-------|---------|
| SID do usuário | identidade do usuário |
| Grupos | SIDs dos grupos do usuário |
| Privilégios | direitos especiais (SeDebugPrivilege, etc.) |
| DACL padrão | ACL aplicada a objetos criados sem ACL explícita |
| Validade | quando o token expira (raramente usado) |

Token atribuído por `winlogon` + `lsass` após autenticação bem-sucedida.

## Descritores de segurança

Cada objeto protegível possui um **Security Descriptor**:
- **Owner SID**: quem possui o objeto
- **Group SID**: grupo principal (POSIX)
- **DACL** (Discretionary ACL): lista de ACEs que controlam acesso
- **SACL** (System ACL): define auditoria

### ACE (Access Control Entry)

Cada ACE na DACL contém: tipo (Allow/Deny), SID, máscara de direitos. O SRM percorre a DACL em ordem; a primeira ACE com SID coincidente determina o resultado.

## Fluxo de autenticação

1. Usuário pressiona Ctrl+Alt+Del → `winlogon` é notificado (sequência segura de atenção)
2. `winlogon` invoca `lsass` para autenticação
3. `lsass` verifica credenciais contra SAM local ou Active Directory
4. Se bem-sucedido: novo processo shell (`explorer.exe`) com token de acesso

## Monitor de segurança (SRM)

Componente do executivo NT. Toda chamada `OpenXXX` passa pelo SRM:
1. Obtém token do chamador
2. Obtém DACL do objeto
3. Percorre ACEs: primeiro match define acesso (Allow ou Deny)
4. Se todos os direitos pedidos foram concedidos: retorna descritor

## Atenuações modernas (Vista/7/8)

- **UAC** (User Account Control): processos executam com token reduzido; elevação explícita para admin
- **ASLR** (Address Space Layout Randomization): randomiza base de módulos e pilha
- **DEP** (Data Execution Prevention): marca páginas de dados como não-executáveis
- **Stack canaries**: valor aleatório na pilha detecta buffer overflow antes do retorno

## Relações (SPO)

- Segurança no Windows 8 -> usa -> SID
- Token de acesso -> identifica -> sujeito de segurança
- Descritor de segurança -> contém -> DACL e SACL
- SRM -> valida -> acesso a objetos
- [[Gerenciador de Objetos do Windows]] -> aplica -> checagem de acesso
- UAC -> restringe -> token de processos administrativos

