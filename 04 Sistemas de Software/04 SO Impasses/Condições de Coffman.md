---
title: Condições de Coffman
aliases:
  - condições para impasse
  - condições de deadlock
tags:
  - computação/sistemas
  - sistemas-operacionais/impasses
source: "Sistemas Operacionais Modernos, cap. 6"
---

# Condições de Coffman

Quatro condições **necessárias e conjuntas** para que um [[Impasse]] de recursos ocorra (Coffman et al., 1971). Se qualquer uma estiver ausente, nenhum impasse de recurso é possível.

## As quatro condições (texto literal — §6.2.1)

> Coffman et al. (1971) demonstraram que quatro condições têm de ser válidas para haver um impasse (de recurso).

### 1. Condição de exclusão mútua
"Cada recurso está atualmente associado a exatamente um processo ou está disponível."

### 2. Condição de posse e espera
"Processos atualmente de posse de recursos que foram concedidos antes podem solicitar novos recursos."

### 3. Condição de não preempção
"Recursos concedidos antes não podem ser tomados à força de um processo. Eles precisam ser explicitamente liberados pelo processo que os têm."

### 4. Condição de espera circular
"Deve haver uma lista circular de dois ou mais processos, cada um deles esperando por um processo de posse do membro seguinte da cadeia."

> [!note]
> "Todas essas quatro condições devem estar presentes para que um impasse de recurso ocorra. Se uma delas estiver ausente, nenhum impasse de recurso será possível."
> Vale observar que cada condição **relaciona-se com uma política** que um sistema pode ter ou não — são escolhas de projeto.

## Uso na [[Prevenção de Impasses]]

Cada condição corresponde a uma política do sistema. Eliminar qualquer uma delas elimina a possibilidade de impasse:

| Condição | Técnica de eliminação |
|---|---|
| Exclusão mútua | Spooling, recursos somente leitura |
| Posse e espera | Alocar todos os recursos antes de iniciar |
| Não preempção | Preempção forçada (só se recurso for preemptível) |
| Espera circular | Ordem total de aquisição de recursos |

## Relações (SPO)

- Condições de Coffman -> são necessárias para -> [[Impasse]]
- [[Prevenção de Impasses]] -> quebra -> Condições de Coffman
- Exclusão mútua -> pode ser eliminada por -> spooling
- Espera circular -> quebrada por -> ordem total de aquisição
- Não preempção -> só pode ser atacada com -> [[Recursos Preemptíveis e Não Preemptíveis|recursos preemptíveis]]

