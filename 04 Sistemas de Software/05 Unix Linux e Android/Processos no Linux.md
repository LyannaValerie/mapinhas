---
title: Processos no Linux
aliases:
  - processos Linux
  - threads no Linux
  - fork
  - exec
  - clone
tags:
  - computação/sistemas
source: "Sistemas Operacionais Modernos, cap. 10"
created_by: Codex
---

# Processos no Linux

## Definição

Entidades de execução gerenciadas pelo núcleo Linux, compostas por estado de CPU, espaço de endereçamento, descritores abertos, credenciais, sinais e metadados de escalonamento.

## Papel técnico

Linux representa processos e threads por estruturas internas semelhantes. Diferença prática: threads compartilham mais recursos (espaço de endereçamento, descritores) via `clone`; processos independentes isolam esses recursos por padrão.

## Conceitos Fundamentais

Entidade ativa principal no Linux. Cada processo executa um único programa com um thread de controle inicial (pode criar threads adicionais depois).

Linux é **multiprogramado**: centenas/milhares de processos simultâneos. Processos de segundo plano = **daemons** (iniciados por script no boot).

Exemplos de daemons: `cron` (tarefas agendadas), gerenciador de fila de impressão, verificador de memória livre, servidor de email.

## Relações (SPO)

- Processo Linux -> contém -> espaço de endereçamento
- Processo Linux -> possui -> descritores de arquivo
- Thread Linux -> compartilha -> recursos do processo
- `fork` -> cria -> novo processo (duplicata)
- `exec` -> substitui -> imagem do programa
- `clone` -> parametriza -> compartilhamento entre tarefas
- Processo Linux -> recebe -> [[Sinais]]
- Daemon -> executa em -> segundo plano sem terminal

## Chamadas de Sistema para Processos

| Chamada | Descrição |
|---------|-----------|
| `pid = fork()` | Cria processo filho idêntico ao pai |
| `pid = waitpid(pid, &statloc, opts)` | Espera filho terminar |
| `s = execve(name, argv, envp)` | Substitui imagem de memória do processo |
| `exit(status)` | Termina processo, retorna status |
| `s = sigaction(sig, &act, &oldact)` | Define tratador de sinal |
| `s = kill(pid, sig)` | Envia sinal para processo |
| `pid = clone(func, stack, flags, arg)` | Cria thread (filho com espaço compartilhado) |

### fork

Duplica o processo: descritores de arquivo, registradores, espaço de endereçamento. Após fork, pai e filho seguem caminhos independentes.

- Retorna **0 no filho**, **PID do filho no pai**
- Espaço de endereçamento copiado via **copy-on-write**: páginas físicas são compartilhadas até escrita

### exec (execve)

Substitui a imagem do processo pelo programa nomeado. Parâmetros: nome do arquivo, vetor de argumentos (`argv`), vetor de ambiente (`envp`).

### Padrão fork + exec + waitpid (shell)

```c
pid = fork();
if (pid == 0) {
    execve(cmd, argv, envp);  // filho: substitui imagem
} else {
    waitpid(pid, &status, 0); // pai: espera filho
}
```

### clone

Cria thread: filho pode compartilhar espaço de endereçamento se `CLONE_VM` estiver em `sharing_flags`.

`sharing_flags` é um mapa de bits:
- `CLONE_VM`: espaço de endereçamento
- `CLONE_FS`: sistema de arquivos (diretório de trabalho)
- `CLONE_SIGHAND`: tratadores de sinal
- `CLONE_FILES`: descritores de arquivo abertos

Cada thread obtém sua própria pilha privada (`stack_ptr`).

## Sinais POSIX

| Sinal | Efeito |
|-------|--------|
| `SIGABRT` | Aborta processo (core dump) |
| `SIGALRM` | Alarme do relógio disparou |
| `SIGFPE` | Erro de ponto flutuante (divisão por 0) |
| `SIGHUP` | Linha telefônica/sessão encerrada |
| `SIGILL` | Tecla DEL — interrompe processo |
| `SIGKILL` | Mata processo (não pode ser capturado/ignorado) |
| `SIGPIPE` | Escrita em pipe sem leitores |
| `SIGSEGV` | Referência a endereço inválido |
| `SIGTERM` | Solicitação elegante de término |
| `SIGUSR1/2` | Uso definido pela aplicação |

## Implementação: task_struct

O processo no núcleo é representado por `task_struct` contendo:
- Informações de escalonamento (prioridade, tempo CPU)
- Imagem de memória (ponteiros para segmentos)
- Sinais (máscara, ações pendentes, bits e flags)
- Descritores de arquivo abertos
- Pilha de modo núcleo e contador de programa de modo núcleo
- Informações de namespace e cgroup

## Inicialização do Linux

```
BIOS → Bootloader (GRUB) → Núcleo
→ Processo 0 (swapper/idle)
→ Processo 1 (init)
→ getty em cada terminal → login → shell
→ Daemons de sistema (paginação, cron, etc.)
```

## Ver também

- [[Processo]]
- [[Thread]]
- [[Sinais]]
- [[Escalonamento no Linux]]
- [[Gerenciamento de Memória no Linux]]
- [[Chamadas de Sistema POSIX]]
- [[Espaço de Usuário e Espaço de Kernel]]
