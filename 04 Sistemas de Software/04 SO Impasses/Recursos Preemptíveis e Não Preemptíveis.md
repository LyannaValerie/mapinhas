---
title: Recursos Preemptíveis e Não Preemptíveis
aliases:
  - preemptible resources
  - recurso preemptível
  - recurso não preemptível
tags:
  - computação/sistemas
  - sistemas-operacionais/impasses
source: "Sistemas Operacionais Modernos, cap. 6"
---

# Recursos Preemptíveis e Não Preemptíveis

**Recurso** é qualquer coisa que precisa ser adquirida, usada e liberada com o passar do tempo — dispositivo de hardware (ex.: unidade Blu-ray) ou fragmento de informação (ex.: registro em banco de dados). Para alguns recursos várias instâncias idênticas existem; qualquer uma delas pode satisfazer qualquer pedido.

## Tipos

### Preemptível
Pode ser retirado do processo proprietário sem causar-lhe prejuízo algum.

- **Memória** é o exemplo canônico: em um PC padrão, as páginas sempre podem ser enviadas para o disco para depois recuperá-las.
- *Exemplo do livro*: Sistema com memória de 1 GB, impressora e dois processos de 1 GB que querem imprimir. Processo A ganha a impressora e começa a computar; excede parcela de tempo e vai para disco. Processo B tenta pegar a impressora sem sucesso — potencial impasse (A tem impressora, B tem memória). Solução: tomar a memória de B por preempção, enviar B ao disco, restaurar A. A imprime e libera a impressora. Nenhum impasse ocorre.
- *Contexto importa*: em smartphone sem suporte a swapping/paginação, memória não é preemptível.

### Não Preemptível
Não pode ser tomado do proprietário atual sem potencialmente causar uma falha.

- **Gravador Blu-ray** em meio a uma gravação: tirar o gravador de repente e dá-lo a outro processo resulta em um Blu-ray bagunçado. Gravadores Blu-ray não são preemptíveis em um momento arbitrário.
- Registros em bancos de dados e tabelas do SO: precisam ser travados para uso — não podem ser virtualizados.

> [!important]
> "Em geral, impasses envolvem recursos não preemptíveis. Impasses potenciais que envolvem recursos preemptíveis normalmente podem ser solucionados realocando recursos de um processo para outro."

## Sequência de uso

Todo recurso segue o ciclo:
1. **Solicitar** o recurso.
2. **Usar** o recurso.
3. **Liberar** o recurso.

Se o recurso não está disponível quando solicitado, o processo é forçado a esperar. Em alguns SOs é automaticamente bloqueado e despertado quando disponível; em outros, a solicitação falha com código de erro e o processo tenta novamente em laço.

Implementação típica com semáforo:
```c
typedef int semaphore;
semaphore resource_1;

void process_A(void) {
    down(&resource_1);  // solicitar
    use_resource_1();   // usar
    up(&resource_1);    // liberar
}
```

## Relações (SPO)

- Recurso preemptível -> conflito resolvido por -> realocação
- Recurso não preemptível -> pode gerar -> [[Impasse]]
- [[Impasse]] -> envolve principalmente -> recursos não preemptíveis
- semáforo -> implementa -> ciclo solicitar-usar-liberar
